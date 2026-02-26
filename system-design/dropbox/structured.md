# System Design Interview Preparation

## Session 5: Dropbox (Cloud File Storage)

---

## 1. Session Context & Goal

**Purpose:**

* Week 5 of system design interview preparation
* Practice designing a **cloud-based file storage and synchronization service** (Dropbox)
* Focus on large file handling, cross-device synchronization, and scalability

**Problem Statement:**
Design a **cloud-based file storage service** (like Dropbox) that allows users to store, share, and synchronize files across multiple devices. The system should provide secure, reliable, and low-latency access to files from anywhere.

---

## 2. Assumptions & Scope

* Users can upload and download files from any device
* Files are synchronized automatically across all linked devices
* System must handle large files (up to 50 GB)
* Focus on storage, sync, and retrieval — not collaboration features (e.g., real-time editing)

**Out of Scope:**
* Authentication and authorization
* Real-time collaborative editing
* File versioning and conflict resolution

---

## 3. Requirements

### 3.1 Functional Requirements

* Upload files to the cloud storage
* Download files from the cloud storage
* Automatically synchronize files across multiple devices
* Share files with other users

---

### 3.2 Non-Functional Requirements

* **Availability over consistency:** Files must always be accessible; eventual consistency is acceptable (a few minutes delay for sync is OK)
* **Low latency:** Uploading and downloading should be as fast as possible
* **Large file support:** Handle files up to 50 GB with resumable uploads
* **Data integrity:** Synchronization must be accurate — no data corruption or loss

---

## 4. Scale & Capacity Assumptions

* Daily active users (DAU): 10 million
* Read/write ratio: 80% downloads / 20% uploads
* Uploading users: ~2 million per day
* Files uploaded per user per day: ~5 files
* Average file size: ~50 MB (realistic estimate)
* Data retention: 1–3 years

---

## 5. Capacity Estimation

### Storage Calculation

```
Uploading users per day:     2 million
Files per user per day:      5
Average file size:           50 MB
Daily new storage:           2M × 5 × 50 MB = 500 TB/day

1-year retention:            500 TB × 365 ≈ ~180 PB
3-year retention:            ~500 PB
```

> Note: These are extreme upper-bound estimates. In an interview, the interviewer would provide more realistic numbers. The key takeaway is that storage requirements are massive and require scalable blob storage (e.g., S3).

### Upload Time Estimation (Large Files)

```
File size:                   50 GB
Network speed:               100 Mbps
Upload time:                 50 GB × 8 / 100 Mbps ≈ 4,000 seconds ≈ ~1 hour 10 minutes
```

This confirms the need for chunked, resumable uploads.

---

## 6. Core Entities

### File (Raw Bytes)

* The actual file content stored in blob storage

### File Metadata

* File ID
* File name
* User ID (owner)
* File URL (presigned URL for direct access)
* Chunks list (for large files)
* Upload timestamp
* Sync status

### Chunk

* Chunk ID (fingerprint/hash)
* Status (uploaded / in-progress)
* S3 link

### User

* User ID
* Linked devices

---

## 7. System Interface (API)

### Upload File

```
POST /files
Body: { file, metadata }
Response: 200 OK
```

### Download File

```
GET /files/{fileId}
Response: { file, metadata }
```

### Get Recent Changes

```
GET /files/changes?since={timestamp}
Response: { fileIds: [...] }
```

---

## 8. High-Level Design (HLD)

### Components

* **Client Application:** Desktop/mobile app with rich logic — chunking, local storage, change detection, sync
* **Local Folder:** User's synced folder on their device
* **Load Balancer + API Gateway:** Routing, SSL termination, authentication (JWT/cookies), request distribution
* **File Service:** Handles file upload/download, metadata CRUD, presigned URL generation
* **Sync Service:** Tracks and returns file changes since a given timestamp for cross-device synchronization
* **Blob Storage (S3):** Stores raw file bytes (chunks)
* **Metadata Storage (DynamoDB):** Stores structured file metadata — scalable to millions of requests/second

### Architecture Flow

```
Client Application ←→ Local Folder (user's device)
        │
        ▼
Load Balancer + API Gateway
  (SSL, Auth, Routing)
        │
        ▼
┌───────────────────────────┐
│       API Server          │
│  ┌─────────┐ ┌─────────┐ │
│  │  File    │ │  Sync   │ │
│  │ Service  │ │ Service │ │
│  └────┬─────┘ └────┬────┘ │
└───────┼────────────┼──────┘
        │            │
        ▼            ▼
┌──────────────┐  ┌──────────────┐
│  Blob Storage│  │   Metadata   │
│    (S3)      │  │   Storage    │
│  (raw bytes) │  │  (DynamoDB)  │
└──────────────┘  └──────────────┘
        ▲
        │ (presigned URL - direct upload/download)
        │
Client Application
```

---

## 9. Deep Dive: Chunked File Upload

### Why Chunking?

* HTTP body size limits (~5 MB per request)
* Resumable uploads — if connection drops, resume from last successful chunk
* Delta sync — only upload changed chunks, not the entire file

### Upload Flow

1. Client application splits file into 5 MB chunks
2. Client calculates hash (fingerprint) for each chunk
3. Client uploads each chunk to blob storage (directly via presigned URL)
4. File metadata stores the chunk list with: chunk ID, status, S3 link
5. On completion, file is marked as fully uploaded

### Download Flow

1. Client requests file metadata (including chunk list)
2. Client downloads each chunk directly from S3 via presigned URLs
3. Client reassembles chunks into the original file

### Delta Sync (Efficient Updates)

* When a file is modified, client compares chunk fingerprints
* Only changed chunks are re-uploaded
* Significantly reduces bandwidth for large files with small edits

---

## 10. Deep Dive: Client Application

### Responsibilities

* **File chunking:** Split files into 5 MB chunks for upload, reassemble on download
* **Local change detection:** Monitor local folder for file modifications
* **Fingerprint comparison:** Compare chunk hashes to determine which chunks need re-uploading
* **Sync polling:** Periodically request changes from sync service
* **Local storage/DB:** Cache metadata and chunk information locally to avoid redundant server requests
* **File reassembly:** Combine downloaded chunks back into files

---

## 11. Deep Dive: Presigned URLs (Low Latency)

### Download Optimization

* File service generates presigned S3 URLs
* Client downloads directly from S3, bypassing the application servers
* Eliminates the bottleneck of routing large files through internal services

### Upload Optimization

* Client uploads chunks directly to S3 via presigned URLs
* File service only handles metadata and URL generation
* Dramatically reduces upload latency and server load

---

## 12. Deep Dive: Load Balancer & API Gateway

### Expanded Architecture

```
Client Application
        │
        ▼
┌─────────────────────┐
│    Load Balancer     │
│ (global, multi-region)│
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│    API Gateway       │
│ (AWS API Gateway)    │
│ - Authentication     │
│ - JWT/Cookie check   │
│ - Rate limiting      │
│ - Request routing    │
└─────────┬───────────┘
          ▼
┌─────────────────────┐
│    API Server        │
│ (multi-region)       │
│ - File Service       │
│ - Sync Service       │
│ - Presigned URL gen  │
└─────────────────────┘
```

### Key Decisions

* **Multi-region deployment:** API servers deployed in different regions to minimize request latency
* **AWS API Gateway:** Scalable, distributed, handles heavy load from 10 million users
* **Separation of concerns:** Load balancer handles distribution, API gateway handles auth and routing

---

## 13. Technology Choices

### Blob Storage: AWS S3

* Handles petabyte-scale data
* Supports presigned URLs for direct client access
* Highly available and durable (99.999999999% durability)

### Metadata Storage: DynamoDB

* Highly scalable NoSQL database
* Supports millions of requests per second
* Efficient key-based lookups for file metadata

### API Gateway: AWS API Gateway

* Distributed and scalable
* Built-in authentication, rate limiting, SSL
* Multi-region support

---

## 14. Requirements Fulfillment

| Requirement | Solution |
|-------------|----------|
| Upload files | File Service + chunked upload to S3 |
| Download files | Presigned URLs for direct S3 download |
| Sync across devices | Sync Service + client-side change detection |
| Availability | S3 durability + DynamoDB availability |
| Low latency | Presigned URLs (direct S3 access), multi-region deployment |
| Large file support | 5 MB chunked uploads with resumable capability |
| Data integrity | Chunk fingerprints + sync service accuracy |

---

## 15. Summary & Reflection

* Designed a scalable cloud file storage system supporting files up to 50 GB
* Key insight: Presigned URLs for direct S3 access eliminate server bottlenecks for upload/download
* Chunked uploads enable resumability and delta sync (only upload changed chunks)
* Client application carries significant logic: chunking, fingerprinting, local caching, change detection
* DynamoDB provides the scalability needed for metadata at 10 million DAU
* Sync service enables cross-device file synchronization with eventual consistency
* Next improvement: Deep dive into conflict resolution, file versioning, and sharing permissions

---

*End of Session 5*
