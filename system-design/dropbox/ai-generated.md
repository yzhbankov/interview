# Dropbox (Cloud File Storage & Sync) System Design
## FAANG Senior Engineer Level

---

## 1. Problem Clarification & Scope

### Core Problem
Design a **cloud-based file storage and synchronization service** (like Dropbox, Google Drive, or OneDrive) that enables users to upload, download, store, and synchronize files seamlessly across multiple devices. The system must handle large files efficiently, maintain data integrity, and provide low-latency access from anywhere in the world.

### Clarifying Questions to Ask
- What's the maximum file size we need to support? (Assume up to 50 GB)
- Do we need real-time collaboration (e.g., simultaneous editing)?
- Should we support file versioning and rollback?
- What's the expected read/write ratio?
- Do we need to support file sharing with non-registered users (public links)?
- Should we handle conflict resolution for simultaneous edits?
- What's the data retention policy?
- Do we need offline support?

### Scope Definition

**In Scope:**
- File upload and download with resumable support
- Automatic file synchronization across devices
- Large file handling (up to 50 GB) via chunked transfers
- File metadata management
- Low-latency access via presigned URLs
- Scalable storage and metadata infrastructure

**Out of Scope (mention but don't design):**
- Authentication and authorization (assume handled by separate service)
- Real-time collaborative editing
- File versioning and conflict resolution (mention as extension)
- Sharing and permission management (mention as extension)
- Full-text search within files
- Encryption at rest and in transit (mention as important but not the focus)

---

## 2. Requirements

### Functional Requirements

| Requirement | Priority | Description |
|-------------|----------|-------------|
| Upload files | P0 | Users can upload files to cloud storage |
| Download files | P0 | Users can download files from cloud storage |
| Sync across devices | P0 | Files automatically synchronize across all linked devices |
| Resumable uploads | P0 | Large file uploads can resume after interruption |
| File sharing | P1 | Users can share files with others |
| Change notification | P1 | Devices are notified of file changes for sync |
| Delta sync | P2 | Only changed portions of files are synced |

### Non-Functional Requirements

| Requirement | Target | Rationale |
|-------------|--------|-----------|
| Availability | 99.99% uptime | Files must always be accessible |
| Consistency | Eventual (AP system) | Availability prioritized over immediate consistency |
| Latency | < 200ms for metadata ops | Fast sync and file listing |
| Upload/download speed | Near network bandwidth | Direct S3 access via presigned URLs |
| File size | Up to 50 GB | Enterprise use cases |
| Durability | 99.999999999% (11 nines) | No data loss, ever |
| Data integrity | Bit-perfect sync | Chunk fingerprints ensure accuracy |

### Failure Mode Decision

| Component | Failure Impact | Mitigation | Decision |
|-----------|----------------|------------|----------|
| Blob storage (S3) | Files inaccessible | Multi-AZ replication | Highly durable |
| Metadata DB | Can't list/find files | DynamoDB multi-region | Auto-failover |
| File service | Upload/download fails | Multiple instances + LB | Retry with backoff |
| Sync service | Devices out of sync | Queue-based, idempotent | Catch-up on recovery |
| Client app crash | Local changes lost | Write-ahead log locally | Resume from last state |

---

## 3. Capacity Estimation

### Traffic Assumptions
```
Daily active users (DAU):    10 million
Upload ratio:                20% of DAU = 2 million uploaders
Download ratio:              80% of DAU = 8 million downloaders
Files per uploader per day:  5 files
Average file size:           50 MB (with heavy tail up to 50 GB)
```

### Storage Calculation

#### Daily Ingestion
```
Daily uploads:               2M users × 5 files × 50 MB = 500 TB/day
Monthly uploads:             500 TB × 30 = 15 PB/month
Yearly storage:              ~180 PB/year
```

#### With Deduplication (estimated 30% savings)
```
Effective yearly storage:    ~125 PB/year
```

> **Interview Note:** These are upper-bound estimates using extreme assumptions. Real-world numbers would be lower. The key insight is that the system needs petabyte-scale blob storage — S3 is the natural choice.

### Bandwidth Calculation
```
Upload bandwidth:            500 TB / 86,400 sec ≈ 5.8 GB/sec ≈ 46 Gbps
Download bandwidth (4x):     ~23 GB/sec ≈ 185 Gbps
Total peak bandwidth:        ~230 Gbps
```

### Upload Time for Large Files
```
File size:        50 GB
Network speed:    100 Mbps (typical home connection)
Upload time:      50 GB × 8 bits / 100 Mbps = 4,000 sec ≈ 1 hour 7 minutes
Chunk size:       5 MB
Total chunks:     50 GB / 5 MB = 10,000 chunks
```

### Metadata Storage
```
Files per user:              ~1,000 (lifetime average)
Total files:                 10M users × 1,000 = 10 billion file records
Metadata per file:           ~500 bytes
Total metadata:              10B × 500 bytes = 5 TB
```

---

## 4. Core Entities & Data Model

### Entities

```
┌─────────────────────────────────────────────────────────────┐
│ File Metadata                                                │
├─────────────────────────────────────────────────────────────┤
│ file_id: string            (primary key, UUID)               │
│ user_id: string            (owner, partition key)            │
│ file_name: string          (display name)                    │
│ file_size: long            (total size in bytes)             │
│ file_hash: string          (SHA-256 of complete file)        │
│ mime_type: string          (content type)                    │
│ status: enum               (uploading | complete | deleted)  │
│ s3_path: string            (blob storage location)           │
│ chunks: list<ChunkMeta>    (ordered list of chunks)          │
│ created_at: timestamp                                        │
│ updated_at: timestamp                                        │
│ last_synced: timestamp                                       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Chunk Metadata                                               │
├─────────────────────────────────────────────────────────────┤
│ chunk_id: string           (chunk fingerprint/hash)          │
│ file_id: string            (parent file)                     │
│ chunk_index: int           (position in file)                │
│ chunk_size: int            (size in bytes)                   │
│ status: enum               (pending | uploaded | verified)   │
│ s3_key: string             (S3 object key)                   │
│ checksum: string           (MD5/SHA for integrity)           │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ User                                                         │
├─────────────────────────────────────────────────────────────┤
│ user_id: string            (primary key)                     │
│ email: string                                                │
│ storage_used: long         (bytes)                           │
│ storage_limit: long        (bytes)                           │
│ devices: list<DeviceInfo>  (linked devices)                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ SyncEvent                                                    │
├─────────────────────────────────────────────────────────────┤
│ event_id: string           (primary key)                     │
│ user_id: string            (partition key)                   │
│ file_id: string            (affected file)                   │
│ event_type: enum           (created | updated | deleted)     │
│ timestamp: long            (sort key for efficient queries)  │
│ device_id: string          (originating device)              │
└─────────────────────────────────────────────────────────────┘
```

### Storage Key Patterns

```
# S3 paths (partitioned by user and file)
s3://dropbox-files/{user_id}/{file_id}/{chunk_index}.chunk

# DynamoDB tables
FileMetadata:     PK=user_id, SK=file_id
ChunkMetadata:    PK=file_id, SK=chunk_index
SyncEvents:       PK=user_id, SK=timestamp

# Cache keys (Redis)
presigned:{file_id}:{chunk_index}  →  {presigned_url, expires_at}
sync:cursor:{user_id}:{device_id}  →  {last_sync_timestamp}
```

---

## 5. System Interface (API)

### File Upload (Initiate)

```
POST /api/v1/files/upload
Content-Type: application/json

Request:
{
    "file_name": "report.pdf",
    "file_size": 52428800,        // 50 MB in bytes
    "file_hash": "sha256:abc...",
    "chunk_count": 10
}

Response: 200 OK
{
    "file_id": "f-123-456",
    "upload_urls": [               // Presigned S3 URLs for each chunk
        { "chunk_index": 0, "upload_url": "https://s3.../chunk0?X-Amz-..." },
        { "chunk_index": 1, "upload_url": "https://s3.../chunk1?X-Amz-..." },
        ...
    ]
}
```

### File Upload (Complete)

```
POST /api/v1/files/{fileId}/complete
Content-Type: application/json

Request:
{
    "chunks": [
        { "chunk_index": 0, "checksum": "md5:abc...", "etag": "..." },
        { "chunk_index": 1, "checksum": "md5:def...", "etag": "..." },
        ...
    ]
}

Response: 200 OK
{
    "file_id": "f-123-456",
    "status": "complete"
}
```

### File Download

```
GET /api/v1/files/{fileId}

Response: 200 OK
{
    "file_id": "f-123-456",
    "file_name": "report.pdf",
    "file_size": 52428800,
    "download_urls": [             // Presigned S3 URLs for each chunk
        { "chunk_index": 0, "url": "https://s3.../chunk0?X-Amz-..." },
        { "chunk_index": 1, "url": "https://s3.../chunk1?X-Amz-..." },
        ...
    ],
    "metadata": { ... }
}
```

### Get Changes (Sync)

```
GET /api/v1/files/changes?since={timestamp}&device_id={deviceId}

Response: 200 OK
{
    "changes": [
        {
            "file_id": "f-123-456",
            "event_type": "updated",
            "timestamp": 1708876543,
            "file_name": "report.pdf"
        },
        ...
    ],
    "cursor": "next-timestamp-value"
}
```

---

## 6. High-Level Architecture

### Architecture Diagram

```
┌────────────────────────────────────────────────────────────────────────┐
│                        Client Application                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐                 │
│  │  File Watcher │  │ Chunk Engine │  │  Sync Engine │                 │
│  │ (detect local│  │ (split/merge │  │ (poll changes│                 │
│  │  changes)    │  │  chunks)     │  │  from server)│                 │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘                 │
│         │                 │                 │                          │
│  ┌──────▼─────────────────▼─────────────────▼───────┐                 │
│  │              Local SQLite Database                 │                 │
│  │  (chunk hashes, file metadata, sync cursors)       │                 │
│  └───────────────────────────────────────────────────┘                 │
└──────────────────┬───────────────────────┬─────────────────────────────┘
                   │                       │
         ┌─────────▼────────┐    ┌─────────▼────────────┐
         │  Direct S3 Access │    │   API Requests        │
         │  (presigned URLs) │    │   (metadata, sync)    │
         │                   │    │                       │
         │  Upload chunks ▲  │    │                       │
         │  Download chunks  │    │                       │
         └─────────┬─────────┘    └─────────┬─────────────┘
                   │                        │
                   ▼                        ▼
         ┌─────────────────┐    ┌───────────────────────────┐
         │   Blob Storage   │    │     Load Balancer          │
         │   (AWS S3)       │    │  (Global, Multi-Region)    │
         │                  │    └───────────┬───────────────┘
         │  ┌────────────┐  │                │
         │  │ File Chunks│  │    ┌───────────▼───────────────┐
         │  │ (5 MB each)│  │    │      API Gateway           │
         │  └────────────┘  │    │  (AWS API Gateway)         │
         └──────────────────┘    │  - Authentication          │
                                 │  - Rate Limiting           │
                                 │  - SSL Termination         │
                                 │  - Request Routing         │
                                 └───────────┬───────────────┘
                                             │
                                 ┌───────────▼───────────────┐
                                 │       API Server           │
                                 │   (Multi-Region Fleet)     │
                                 │                            │
                                 │  ┌─────────────────────┐   │
                                 │  │    File Service      │   │
                                 │  │  - Upload initiation │   │
                                 │  │  - Presigned URL gen │   │
                                 │  │  - Metadata CRUD     │   │
                                 │  │  - Upload completion │   │
                                 │  └──────────┬──────────┘   │
                                 │             │              │
                                 │  ┌──────────▼──────────┐   │
                                 │  │    Sync Service      │   │
                                 │  │  - Change tracking   │   │
                                 │  │  - Device sync state │   │
                                 │  │  - Change feed       │   │
                                 │  └──────────┬──────────┘   │
                                 └─────────────┼──────────────┘
                                               │
                              ┌────────────────┴────────────────┐
                              │                                  │
                   ┌──────────▼──────────┐         ┌────────────▼────────────┐
                   │   Metadata Storage   │         │   Notification Service  │
                   │   (DynamoDB)         │         │   (Optional: WebSocket  │
                   │                      │         │    or Push Notification)│
                   │  - File metadata     │         └─────────────────────────┘
                   │  - Chunk metadata    │
                   │  - Sync events       │
                   │  - User data         │
                   └──────────────────────┘
```

### Request Flows

#### Upload Flow
```
1. Client detects new/modified file in local folder
2. Client chunks file into 5 MB pieces
3. Client computes hash (fingerprint) for each chunk
4. Client sends POST /files/upload with file metadata to API server
5. File Service creates file record in DynamoDB (status: "uploading")
6. File Service generates presigned S3 URLs for each chunk
7. File Service returns upload URLs to client
8. Client uploads each chunk directly to S3 via presigned URLs
9. Client sends POST /files/{id}/complete with chunk checksums
10. File Service verifies chunks and updates status to "complete"
11. File Service writes SyncEvent to DynamoDB
12. Other devices pick up change on next sync poll
```

#### Download Flow
```
1. Client detects new file via sync polling (GET /files/changes)
2. Client requests file metadata (GET /files/{id})
3. File Service generates presigned download URLs for each chunk
4. Client downloads chunks directly from S3 in parallel
5. Client verifies chunk checksums
6. Client reassembles chunks into complete file
7. Client updates local database with file metadata
```

#### Sync Flow
```
1. Client periodically polls GET /files/changes?since={last_cursor}
2. Sync Service queries SyncEvents table for changes after cursor
3. Returns list of created/updated/deleted files
4. Client processes each change:
   a. Created → download new file
   b. Updated → download only changed chunks (delta sync)
   c. Deleted → remove local file
5. Client updates sync cursor
```

---

## 7. Deep Dive: Chunked Upload & Resumable Transfers

### Why Chunking is Essential

| Problem | Solution via Chunking |
|---------|-----------------------|
| HTTP body size limits (~5 MB) | Each chunk fits within limits |
| Network interruptions | Resume from last successful chunk |
| Bandwidth waste on edits | Only re-upload changed chunks |
| Memory constraints | Process file in streaming fashion |
| Parallel upload | Multiple chunks uploaded concurrently |

### Chunk Strategy

```python
class ChunkEngine:
    CHUNK_SIZE = 5 * 1024 * 1024  # 5 MB

    def split_file(self, file_path: str) -> List[Chunk]:
        """
        Splits file into fixed-size chunks with fingerprints.
        """
        chunks = []
        with open(file_path, 'rb') as f:
            index = 0
            while True:
                data = f.read(self.CHUNK_SIZE)
                if not data:
                    break
                chunk_hash = hashlib.sha256(data).hexdigest()
                chunks.append(Chunk(
                    index=index,
                    size=len(data),
                    hash=chunk_hash,
                    data=data
                ))
                index += 1
        return chunks

    def get_changed_chunks(
        self,
        local_chunks: List[Chunk],
        remote_chunks: List[ChunkMeta]
    ) -> List[Chunk]:
        """
        Compares local and remote chunk hashes to find changes.
        Only returns chunks that need re-uploading.
        """
        remote_hashes = {c.index: c.hash for c in remote_chunks}
        changed = []
        for chunk in local_chunks:
            if (chunk.index not in remote_hashes or
                chunk.hash != remote_hashes[chunk.index]):
                changed.append(chunk)
        return changed
```

### Resumable Upload State Machine

```
            ┌──────────┐
            │ INITIATED │
            └─────┬─────┘
                  │ POST /files/upload
                  ▼
            ┌──────────┐
         ┌──│ UPLOADING │──┐
         │  └─────┬─────┘  │
         │        │        │ (connection lost)
         │        │        ▼
         │        │  ┌──────────┐
         │        │  │  PAUSED  │
         │        │  └─────┬────┘
         │        │        │ (resume: upload remaining chunks)
         │        │        │
         │        ◄────────┘
         │        │
         │        │ (all chunks uploaded)
         │        ▼
         │  ┌──────────┐
         │  │COMPLETING │
         │  └─────┬─────┘
         │        │ POST /files/{id}/complete
         │        ▼
         │  ┌──────────┐
         └──│ COMPLETE  │
            └──────────┘
```

---

## 8. Deep Dive: Synchronization Service

### Sync Architecture

The sync service is the backbone of cross-device file consistency. It tracks every file change as an event and allows devices to efficiently fetch only what's changed.

### Event-Driven Sync Model

```python
class SyncService:
    def record_change(self, user_id: str, file_id: str,
                      event_type: str, device_id: str):
        """
        Records a file change event in DynamoDB.
        Called by File Service after upload/delete completes.
        """
        event = {
            'user_id': user_id,              # Partition key
            'timestamp': int(time.time_ns()),  # Sort key (nanoseconds)
            'file_id': file_id,
            'event_type': event_type,         # created | updated | deleted
            'device_id': device_id
        }
        self.dynamo.put_item(TableName='SyncEvents', Item=event)

    def get_changes(self, user_id: str, since_timestamp: int,
                    device_id: str) -> dict:
        """
        Returns all file changes for a user since the given timestamp.
        Excludes changes originating from the requesting device.
        """
        response = self.dynamo.query(
            TableName='SyncEvents',
            KeyConditionExpression='user_id = :uid AND timestamp > :ts',
            FilterExpression='device_id <> :did',
            ExpressionAttributeValues={
                ':uid': user_id,
                ':ts': since_timestamp,
                ':did': device_id
            }
        )
        return {
            'changes': response['Items'],
            'cursor': response['Items'][-1]['timestamp'] if response['Items'] else since_timestamp
        }
```

### Polling vs. Push Notification

| Approach | Pros | Cons | When to Use |
|----------|------|------|-------------|
| **Long polling** | Simple, reliable | Higher latency, more requests | Default approach |
| **WebSocket** | Real-time updates | Connection management overhead | Desktop clients |
| **Push notification** | Battery-efficient | Unreliable delivery | Mobile clients |

**Recommended:** Start with long polling for simplicity. Add WebSocket connections for desktop clients that need real-time sync. Use push notifications to wake mobile clients.

---

## 9. Deep Dive: Client Application Architecture

### Client Components

```
┌──────────────────────────────────────────────────────────┐
│                   Client Application                      │
│                                                           │
│  ┌────────────────┐    ┌────────────────────────────┐    │
│  │  File Watcher   │    │     Chunk Engine            │    │
│  │                 │    │                              │    │
│  │ - Monitor local │    │ - Split files into chunks    │    │
│  │   folder (inotify│   │ - Compute chunk hashes       │    │
│  │   / FSEvents)   │    │ - Compare with remote hashes │    │
│  │ - Detect creates,│   │ - Reassemble downloaded      │    │
│  │   updates, deletes│  │   chunks into files          │    │
│  └────────┬────────┘    └──────────┬─────────────────┘    │
│           │                        │                      │
│  ┌────────▼────────────────────────▼────────────────┐    │
│  │              Sync Controller                       │    │
│  │                                                    │    │
│  │ - Coordinate upload/download flows                 │    │
│  │ - Poll server for remote changes                   │    │
│  │ - Resolve ordering of operations                   │    │
│  │ - Retry failed transfers with backoff              │    │
│  └────────────────────┬──────────────────────────────┘    │
│                       │                                   │
│  ┌────────────────────▼──────────────────────────────┐    │
│  │              Local Database (SQLite)                │    │
│  │                                                    │    │
│  │ - File metadata cache                              │    │
│  │ - Chunk hash index                                 │    │
│  │ - Sync cursor per device                           │    │
│  │ - Upload/download queue                            │    │
│  │ - Pending operations log                           │    │
│  └────────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────┘
```

### Delta Sync Algorithm

```python
class DeltaSyncEngine:
    """
    Efficiently syncs files by only transferring changed chunks.
    """

    def sync_file_upload(self, file_path: str, file_id: str):
        """
        Uploads only the chunks that have changed.
        """
        # 1. Split local file into chunks
        local_chunks = self.chunk_engine.split_file(file_path)

        # 2. Get remote chunk metadata
        remote_metadata = self.api.get_file_metadata(file_id)
        remote_chunks = remote_metadata['chunks']

        # 3. Find changed chunks by comparing hashes
        changed = self.chunk_engine.get_changed_chunks(
            local_chunks, remote_chunks
        )

        if not changed:
            return  # No changes

        # 4. Request presigned URLs only for changed chunks
        upload_urls = self.api.get_upload_urls(
            file_id,
            chunk_indices=[c.index for c in changed]
        )

        # 5. Upload changed chunks in parallel
        with ThreadPoolExecutor(max_workers=4) as executor:
            futures = []
            for chunk in changed:
                url = upload_urls[chunk.index]
                futures.append(
                    executor.submit(self._upload_chunk, url, chunk)
                )

            # Wait for all uploads to complete
            for future in futures:
                future.result()

        # 6. Complete upload
        self.api.complete_upload(file_id, local_chunks)

        # 7. Update local database
        self.local_db.update_file_chunks(file_id, local_chunks)
```

---

## 10. Deep Dive: Presigned URLs & Direct S3 Access

### Why Presigned URLs?

Without presigned URLs, every file upload/download would flow through application servers:
```
Client → Load Balancer → API Server → S3    (slow, expensive, bottleneck)
```

With presigned URLs:
```
Client → S3 directly                          (fast, scalable, cheap)
```

### Presigned URL Generation

```python
class PresignedURLService:
    """
    Generates time-limited presigned URLs for direct S3 access.
    """

    UPLOAD_EXPIRY = 3600      # 1 hour
    DOWNLOAD_EXPIRY = 900     # 15 minutes

    def __init__(self, s3_client):
        self.s3 = s3_client

    def generate_upload_url(self, user_id: str, file_id: str,
                            chunk_index: int) -> str:
        """
        Generates a presigned PUT URL for uploading a chunk.
        """
        key = f"{user_id}/{file_id}/{chunk_index}.chunk"
        url = self.s3.generate_presigned_url(
            'put_object',
            Params={
                'Bucket': 'dropbox-files',
                'Key': key,
                'ContentType': 'application/octet-stream'
            },
            ExpiresIn=self.UPLOAD_EXPIRY
        )
        return url

    def generate_download_url(self, user_id: str, file_id: str,
                              chunk_index: int) -> str:
        """
        Generates a presigned GET URL for downloading a chunk.
        """
        key = f"{user_id}/{file_id}/{chunk_index}.chunk"
        url = self.s3.generate_presigned_url(
            'get_object',
            Params={
                'Bucket': 'dropbox-files',
                'Key': key
            },
            ExpiresIn=self.DOWNLOAD_EXPIRY
        )
        return url

    def generate_batch_urls(self, user_id: str, file_id: str,
                            chunk_count: int,
                            operation: str = 'download') -> List[dict]:
        """
        Generates presigned URLs for all chunks of a file.
        """
        urls = []
        generator = (self.generate_upload_url if operation == 'upload'
                      else self.generate_download_url)

        for i in range(chunk_count):
            urls.append({
                'chunk_index': i,
                'url': generator(user_id, file_id, i)
            })
        return urls
```

### Security Considerations

- Presigned URLs expire after a configurable time (15 min for download, 1 hour for upload)
- URLs are scoped to a specific S3 key — cannot access other files
- HTTPS enforced for all presigned URL access
- Server-side encryption (SSE-S3) protects data at rest

---

## 11. Scalability & Performance

### Multi-Region Deployment

```
┌─────────────────────────────────────────────────────────┐
│                    Global Architecture                    │
│                                                          │
│  US-East              EU-West             AP-Southeast   │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐   │
│  │API Server│        │API Server│        │API Server│   │
│  │Fleet     │        │Fleet     │        │Fleet     │   │
│  └────┬─────┘        └────┬─────┘        └────┬─────┘   │
│       │                   │                   │          │
│  ┌────▼─────┐        ┌────▼─────┐        ┌────▼─────┐   │
│  │DynamoDB  │◄──────►│DynamoDB  │◄──────►│DynamoDB  │   │
│  │(Global   │ Global │(Global   │ Global │(Global   │   │
│  │ Tables)  │ Replic.│ Tables)  │ Replic.│ Tables)  │   │
│  └──────────┘        └──────────┘        └──────────┘   │
│                                                          │
│  ┌──────────┐        ┌──────────┐        ┌──────────┐   │
│  │  S3      │        │  S3      │        │  S3      │   │
│  │(Regional)│        │(Regional)│        │(Regional)│   │
│  └──────────┘        └──────────┘        └──────────┘   │
└─────────────────────────────────────────────────────────┘
```

### Performance Optimizations

| Optimization | Impact | Implementation |
|-------------|--------|----------------|
| Presigned URLs | Eliminates server bottleneck | S3 direct access |
| Parallel chunk upload | 4-8x faster uploads | ThreadPool on client |
| Delta sync | 90%+ bandwidth savings | Chunk hash comparison |
| CDN for downloads | Lower latency globally | CloudFront in front of S3 |
| Connection pooling | Reduced handshake overhead | HTTP/2 keep-alive |
| Compression | Smaller transfer sizes | gzip for compressible files |
| DynamoDB DAX | <1ms metadata reads | In-memory cache layer |

### Handling 10 Million DAU

| Component | Scaling Strategy |
|-----------|-----------------|
| API Gateway | AWS API Gateway — auto-scales |
| API Servers | Auto-scaling group, multi-AZ |
| DynamoDB | On-demand capacity, global tables |
| S3 | Effectively unlimited scale |
| Load Balancer | ALB with cross-zone balancing |

---

## 12. Monitoring & Observability

### Key Metrics

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| `upload.success_rate` | Successful uploads / total | < 99% |
| `upload.latency_p99` | Time to complete upload | > 30s (small files) |
| `download.latency_p99` | Time to first byte | > 500ms |
| `sync.lag_seconds` | Time for change to propagate | > 60s |
| `chunks.failed_count` | Failed chunk uploads | > 0.1% |
| `storage.total_bytes` | Total storage used | Capacity planning |
| `api.error_rate` | 5xx responses | > 1% |
| `presigned.generation_time` | URL generation latency | > 100ms |

---

## 13. Availability & Fault Tolerance

### Data Durability Strategy

| Layer | Durability Mechanism |
|-------|---------------------|
| Client | Local copy of files + SQLite metadata |
| S3 | 11 nines durability, cross-AZ replication |
| DynamoDB | Multi-AZ, continuous backups, global tables |
| Chunks | Each chunk independently stored and verified |

### Failure Recovery

| Failure | Impact | Recovery |
|---------|--------|----------|
| Client crash during upload | Partial upload | Resume from last committed chunk |
| Network interruption | Transfer stops | Client retries with exponential backoff |
| API server crash | Request fails | Load balancer routes to healthy instance |
| S3 temporary unavailability | Upload/download fails | Retry with backoff; S3 is multi-AZ |
| DynamoDB throttling | Metadata ops slow | Auto-scaling + retry |

---

## 14. Summary: Requirements Mapping

| Requirement | Solution | Component |
|-------------|----------|-----------|
| File upload | Chunked upload with presigned URLs | Client + File Service + S3 |
| File download | Presigned download URLs | Client + File Service + S3 |
| Cross-device sync | Event-based sync with polling | Sync Service + DynamoDB |
| Availability (99.99%) | Multi-region, S3 durability | Global architecture |
| Low latency | Direct S3 access, multi-region API | Presigned URLs + CDN |
| Large files (50 GB) | 5 MB chunked transfers | Chunk Engine |
| Resumable uploads | Chunk-level tracking | Client + DynamoDB state |
| Data integrity | SHA-256 fingerprints per chunk | Chunk Engine verification |
| Delta sync | Hash comparison, partial upload | Client-side diff engine |

---

## 15. Interview Discussion Points

### Trade-offs Made

1. **Eventual consistency over strong consistency:** Sync delay is acceptable to maximize availability
2. **Client-side complexity:** Rich client logic (chunking, sync, local DB) reduces server load significantly
3. **Presigned URLs over server-proxied transfers:** Orders of magnitude more scalable, but less control over transfer
4. **Polling over push for sync:** Simpler, more reliable, but slightly higher latency
5. **DynamoDB over relational DB:** Better scalability, but limited query flexibility

### Alternative Approaches Worth Mentioning

- **Rsync-style delta encoding:** Transfer byte-level diffs instead of chunk-level (more complex, more efficient)
- **Content-defined chunking (CDC):** Variable-size chunks based on content boundaries (better dedup across files)
- **Block-level deduplication:** Hash-based dedup across all users (significant storage savings)
- **WebSocket for real-time sync:** Push-based instead of poll-based (lower latency, more complex)
- **CRDT for conflict resolution:** Conflict-free replicated data types for handling concurrent edits

### Questions to Expect

- "How would you handle file conflicts when two users edit the same file?" (Version vectors, last-writer-wins, or prompt user to resolve)
- "How would you implement file sharing?" (Permission table, share links with presigned URLs, ACL on S3 objects)
- "What about file versioning?" (Store previous chunk sets, link versions via metadata)
- "How do you ensure a file isn't corrupted?" (End-to-end checksums: client computes hash, server verifies after all chunks uploaded)
- "How would you optimize for mobile clients?" (Smaller chunks, background sync, wifi-only option, thumbnail previews)
- "What if S3 is temporarily down?" (Client retries with backoff, queue uploads locally, serve from local cache)

### Complexity Extensions

1. **File versioning:** Keep previous chunk sets linked to file metadata, allow rollback
2. **Sharing & permissions:** Permission service with ACLs, shareable presigned URLs
3. **Conflict resolution:** Detect conflicts via version vectors, auto-merge or prompt user
4. **Deduplication:** Global hash table for chunk dedup across users — massive storage savings
5. **Encryption:** Client-side encryption before upload, key management service
6. **Bandwidth throttling:** Client-side rate limiting to avoid saturating user's network

---

*End of Dropbox System Design Guide*
