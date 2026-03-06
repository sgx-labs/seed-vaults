---
title: "File Upload Handling"
tags: [file-upload, multipart, streaming, presigned-url, s3, binary, large-files]
content_type: research
domain: api-design
---

# File Upload Handling

## The Core Idea

File uploads are one of the trickiest parts of API design. Files are large, take time to transfer, and don't fit neatly into JSON request bodies. The right approach depends on file size, how many files, and where they're stored.

## Approach 1: Multipart Form Data

The traditional approach. File is sent as part of a multipart request.

```
POST /api/documents HTTP/1.1
Content-Type: multipart/form-data; boundary=----FormBoundary

------FormBoundary
Content-Disposition: form-data; name="file"; filename="report.pdf"
Content-Type: application/pdf

[binary file data]
------FormBoundary
Content-Disposition: form-data; name="description"

Q4 Financial Report
------FormBoundary--
```

**Pros**: Standard HTTP, well-supported by all clients and frameworks, can send metadata with the file.
**Cons**: Server must buffer or stream the entire file, memory-intensive for large files, hard to show upload progress on the server side.

**When to use**: Small to medium files (under 100MB), simple upload flows, when you need metadata with the file.

### Server-Side Handling
- Set a maximum file size (reject before reading the full body)
- Stream to disk or object storage -- don't buffer in memory
- Validate file type (check magic bytes, not just the extension)
- Generate a unique filename (never trust client-provided filenames)
- Scan for malware before making the file accessible

## Approach 2: Presigned URLs (Direct-to-Storage)

Client gets a temporary upload URL and sends the file directly to storage (S3, GCS, Azure Blob).

```
Step 1: Client requests upload URL
  POST /api/uploads
  {"filename": "report.pdf", "content_type": "application/pdf"}

  Response:
  {
    "upload_url": "https://s3.amazonaws.com/bucket/key?X-Amz-Signature=...",
    "file_id": "file_abc123",
    "expires_in": 300
  }

Step 2: Client uploads directly to storage
  PUT https://s3.amazonaws.com/bucket/key?X-Amz-Signature=...
  Content-Type: application/pdf
  [binary file data]

Step 3: Client confirms upload
  POST /api/uploads/file_abc123/complete
```

**Pros**: No file data passes through your server, scales to any file size, leverages cloud storage infrastructure, your server handles no bandwidth.
**Cons**: More complex client implementation, requires cloud storage, two-step flow.

**When to use**: Large files (100MB+), high-volume uploads, when server bandwidth is a constraint, mobile apps.

## Approach 3: Chunked Upload

File is split into chunks and uploaded sequentially or in parallel.

```
POST /api/uploads
{"filename": "large-video.mp4", "total_size": 2147483648, "chunk_size": 5242880}
Response: {"upload_id": "upl_abc123", "total_chunks": 410}

PUT /api/uploads/upl_abc123/chunks/0
[5MB of binary data]

PUT /api/uploads/upl_abc123/chunks/1
[5MB of binary data]

...

POST /api/uploads/upl_abc123/complete
```

**Pros**: Resumable (failed chunks can be retried), works for very large files, progress tracking per chunk.
**Cons**: Complex server logic, need to track chunk state, reassembly step.

**When to use**: Very large files (1GB+), unreliable networks, when resumability is required.

## Security Considerations

- **Validate file type** server-side. Check magic bytes, not just the extension.
- **Set maximum file size.** Reject oversized uploads at the load balancer or middleware level.
- **Generate unique filenames.** Never use client-provided filenames for storage.
- **Scan for malware** before making uploaded files accessible.
- **Store uploads outside the webroot.** Never serve uploaded files from a directory that executes code.
- **Use Content-Disposition: attachment** when serving downloads to prevent browser execution.

## Comparison

| Factor | Multipart | Presigned URL | Chunked |
|--------|-----------|--------------|---------|
| Max practical file size | 100MB | Unlimited | Unlimited |
| Server bandwidth | High | None | High |
| Client complexity | Low | Medium | High |
| Resumability | No | No* | Yes |
| Progress tracking | Limited | Client-side | Per-chunk |

*S3 multipart upload supports resumability with presigned URLs.

## See Also

- `hub/request-validation.md` -- Validating upload metadata
- `hub/error-handling.md` -- Error responses for failed uploads
- `research/api-security-checklist.md` -- Security for file handling
