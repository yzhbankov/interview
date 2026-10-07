Week 6 of the system design interview preparation. Today we have a system design for ad/click aggregator. The ad/click aggregator is the system that collects and aggregates click data. It is used by advertisers to track ad performance to optimize their companies. These ads are displaying on websites, like for example Facebook. A user can click on these ads, the user will be redirected to the advertiser's website, and the advertiser can then analyze how their ad is working, what are the statistics, get this feedback. And we are building this ad click aggregator.

I will start with the requirements. The functional requirements is what features our system will have. Our aggregator will have the first feature: when the user clicks on an ad, the user will be redirected to the advertiser's website. And when the advertiser wants to understand the metrics of their ad, the advertiser queries the click metrics over time with one-minute granularity — and this is actually really fast feedback. So these are the main functional requirements.

The non-functional requirements: the system should be scalable and should support peaks of 10,000 clicks per second. The system should have low latency for analytic queries, less than one second. It should be fault tolerant. It should have data integrity and idempotency of ad clicks.

During the design we will go back and analyze whether we have implemented all these functional and non-functional requirements. It's probably a good practice to ask the interviewer for some additional information, for example whether we need spam detection, demographic profiling of users, or conversion tracking, etc. But at the moment I'm assuming that these functional and non-functional requirements are good enough to go forward.

The next question, when we have these functional and non-functional requirements, I would ask the interviewer: how many ads does our system support, how many users, how many clicks, etc. Let's focus on 10 million ads at any given time working simultaneously. And we'll have 10,000 clicks per second at the peak.

So: what is the input into our system and what is the output? The input of our system is the click data and advertiser queries. The output is the redirection of the user and aggregated click metrics.

What will be our data flow? User clicks, click data comes into the system, user is redirected. The click data should be validated — we need to detect bot behavior and spam, this is actually a non-functional requirement. We should log click data. We need to aggregate click data. And aggregated data can be queried. That is our flow.

We can talk a little bit about the entities. We have the ad entity: it has a unique ID, a URL where we redirect the user, and probably some metadata. We probably should have the advertiser entity with an ID and some metadata. Additionally we can have the user entity with metadata. These are the core entities.

UID vs ID: UIDs are really good when we have many servers and databases and need data migration — UIDs provide uniqueness across distributed systems. IDs take less space and can be faster in some cases. Since we don't have a specific limitation, UID is probably a good choice here.

Alright, let's start the system design. We have the browser where the user navigates a third-party application and clicks on an ad. The browser will interact first with the click processor. Once the user has clicked something, the browser goes to the click processor. The click processor returns a 302 redirect to the URL. To do that, the click processor reads from the database where the ad is stored — it reads the redirect URL and responds with this redirect URL to the browser. The user navigates to the advertiser's application.

At the same time we need something that logs click data — this click data logger is inside the click processor. And we need the ad placement service. When the browser renders our ad, it goes to the ad placement service. The placement service reads the ad from the ad storage database, which stores the ad UID, metadata, and redirect URL.

As the requirements say, we have 10 million ads at any given time. That's a pretty decent number of records but not something extremely large. For ad storage we can use SQL, for example PostgreSQL, and just store ads there. The ad database will store: ad UID, redirect URL, and other metadata.

When the user clicks on an ad, information goes to the click processor, and the click processor stores this click in the click storage. So I'll add a separate storage for clicks — click DB. We have 10K clicks per second, and let's assume we store all information for one year (data retention = 1 year).

How many records will we have? One year: 365 days × 24 hours × 3600 seconds ≈ 36 million seconds. Multiplied by 10K clicks per second = 360 billion records. That's a lot, and we need storage that can work with this amount of data and also support analytics on top of it. For click DB we can use ClickHouse or Cassandra. Let's use Cassandra. In click DB we store click events: event UID, ad UID, user UID, and timestamp.

On the other end we have the advertiser user. We provide an analytics API to retrieve analytics data. The advertiser makes calls to the analytics API which queries the click database. The advertiser queries the analytics API to understand how their ad is performing.

Functionally so far: first requirement — user clicks and is redirected. Achieved. Second requirement — clicks are saved in click DB and the advertiser can query the analytics API. Achieved. But we have problems.

I'll also add an operational API service for the advertiser to manage their ads — store ad details in our system. Actually, this can be the ad placement service itself. It's probably a better idea to keep the API service separate from the ad placement service used by the browser to read ads.

Now, the analytics problem: the click DB has 360 billion records and analytics queries can be really, really slow. We need something that pre-processes our data. Making these queries every time and aggregating data for the user is resource-consuming, slow, and unnecessary. We need to preprocess the data.

We'll have separate storage that stores pre-aggregated data. Our granularity is one minute. We can aggregate all data based on this one-minute aggregation and store in a so-called OLAP (Online Analytics Processing) database. One minute = 60 seconds, each second 10K clicks. So the OLAP DB will have at most 360 billion / 600K = about 600K records. It's actually a really small number. We can use PostgreSQL for this storage.

We need something intermediate between the click database and the OLAP storage that does the data aggregation: the analytics aggregator service. We need a cron scheduler that will trigger analytics aggregation. The analytics aggregator reads from click DB, aggregates per minute, and stores into the OLAP DB.

In the OLAP DB we store: ad UID, minute (timestamp), total clicks.

This solves the analytics problem and allows us to serve analytics queries with small latency. Now the system is functionally working according to the functional requirements.

Now let's deep dive on fault tolerance and data integrity.

The click processor stores data directly to the click database. But the database is not always available. We need a streaming service here. This service needs to handle 10,000 clicks per second. One suggested service: AWS Kinesis — a highly scalable service with high throughput based on topics. AWS Kinesis can receive data and store it for our database.

Actually, even storing to the click database is unnecessary. We need one-minute granularity — so why store all 360 billion records? We can save space and resources. We can have a service that consumes clicks from the event stream, aggregates this data for one minute, and then each minute stores resulting data to our OLAP database. For example, we can use Apache Flink, which reads data from AWS Kinesis, aggregates data for one minute, then stores directly to the OLAP database.

So we don't need the Cassandra click database at all! Flink will store: ad UID, minute, count of clicks — directly into the OLAP DB. This saves a lot of money, space, and resources. It makes our system more robust and simpler.

AWS Kinesis is highly available and persistent in AWS — we don't need to worry about data loss.

What else can be improved? Our system serves advertisers across multiple geographical regions. Latency is important. We need to place an API gateway between the browser and the ad placement service and click processor. We can make our services horizontally scalable, able to serve more requests per second, and place them near the user to improve user experience.

One of the non-functional requirements is idempotency of ad clicks. When the user clicks multiple times on the same ad, it will initiate multiple clicks, which go through the click processor, to Kinesis, to Flink, to OLAP — and the advertiser gets fake data. We need to identify unique clicks.

Strategies:
1. Track user UID or IP address — if this user already clicked this ad, don't count a second time.
2. Impression ID technique — when the ad placement service provides the ad to the browser, it generates an impression ID. This impression ID is saved in the browser. We generate this impression ID, store it in Redis, and when the click processor receives a click, it reads from Redis to check if this impression has already been clicked. If yes, filter it out. If no, forward to Kinesis.

The impression ID should be secured so clients can't manipulate or generate fake impression IDs. We sign the impression ID with a key (symmetric encryption is good here since it's all internal). The key can be stored in Redis too. When the click processor receives the message, it reads the key from Redis and verifies the signature.

Final review of requirements:

Functional requirements:
- User clicks an ad and is redirected to the advertiser's website: implemented via click processor reading redirect URL from ad DB and returning 302 redirect. ✓
- Advertiser can query click metrics: implemented via analytics API reading from OLAP database. ✓

Non-functional requirements:
- Scalable to support 10K clicks/sec: click processor is stateless and horizontally scalable; API gateway supports multiple instances; AWS Kinesis has high throughput; Flink handles the data processing. ✓
- Low latency analytics query: analytics API queries OLAP database with aggregated data (small number of records); can add indexing and partitioning by ad UID for fast queries. ✓
- Fault tolerance: click processor runs multiple instances; AWS Kinesis is highly available; nothing causes big data loss. ✓
- Idempotency of ad clicks: achieved through impression ID signed with a symmetric key, validated in Redis. ✓

The system supports all functional and non-functional requirements. Thank you.
