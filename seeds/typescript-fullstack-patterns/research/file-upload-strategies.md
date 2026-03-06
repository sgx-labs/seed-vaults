---
title: "File Upload Strategies"
tags: [file-upload, s3, cloudflare-r2, uploadthing, presigned-url, multipart, streaming]
content_type: research
domain: typescript-fullstack
---

# File Upload Strategies

## The Core Idea

Never store uploaded files on your Next.js server's filesystem (serverless functions are ephemeral). Use object storage (S3, R2) and upload directly from the client using presigned URLs. For simpler setups, UploadThing abstracts the complexity.

## UploadThing — The Easy Path

Drop-in file uploads for Next.js. Handles presigned URLs, validation, and callbacks.

```typescript
// lib/uploadthing.ts
import { createUploadthing, type FileRouter } from "uploadthing/next";
import { auth } from "@/auth";

const f = createUploadthing();

export const uploadRouter = {
  avatar: f({ image: { maxFileSize: "2MB", maxFileCount: 1 } })
    .middleware(async () => {
      const session = await auth();
      if (!session) throw new Error("Unauthorized");
      return { userId: session.user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      await db.user.update({
        where: { id: metadata.userId },
        data: { avatarUrl: file.url },
      });
    }),

  document: f({ pdf: { maxFileSize: "16MB" }, "text/plain": { maxFileSize: "1MB" } })
    .middleware(async () => {
      const session = await auth();
      if (!session) throw new Error("Unauthorized");
      return { userId: session.user.id };
    })
    .onUploadComplete(async ({ metadata, file }) => {
      await db.document.create({
        data: { name: file.name, url: file.url, userId: metadata.userId },
      });
    }),
} satisfies FileRouter;
```

```typescript
// Client component
"use client";

import { UploadButton } from "@uploadthing/react";

export function AvatarUpload() {
  return (
    <UploadButton
      endpoint="avatar"
      onClientUploadComplete={(res) => {
        toast.success("Avatar updated");
      }}
      onUploadError={(error) => {
        toast.error(error.message);
      }}
    />
  );
}
```

## Presigned URLs — Direct to S3/R2

For full control. Client gets a presigned URL from your API, then uploads directly to storage.

```typescript
// app/api/upload/route.ts
import { S3Client, PutObjectCommand } from "@aws-sdk/client-s3";
import { getSignedUrl } from "@aws-sdk/s3-request-presigner";

const s3 = new S3Client({
  region: "auto",
  endpoint: process.env.S3_ENDPOINT, // Works for R2, MinIO, S3
  credentials: {
    accessKeyId: process.env.S3_ACCESS_KEY!,
    secretAccessKey: process.env.S3_SECRET_KEY!,
  },
});

export async function POST(request: Request) {
  const { filename, contentType } = await request.json();

  // Validate
  const allowedTypes = ["image/jpeg", "image/png", "image/webp", "application/pdf"];
  if (!allowedTypes.includes(contentType)) {
    return Response.json({ error: "File type not allowed" }, { status: 400 });
  }

  const key = `uploads/${crypto.randomUUID()}/${filename}`;

  const url = await getSignedUrl(
    s3,
    new PutObjectCommand({
      Bucket: process.env.S3_BUCKET!,
      Key: key,
      ContentType: contentType,
    }),
    { expiresIn: 600 } // URL valid for 10 minutes
  );

  return Response.json({ uploadUrl: url, key });
}
```

```typescript
// Client-side upload
async function uploadFile(file: File) {
  // Step 1: Get presigned URL
  const { uploadUrl, key } = await fetch("/api/upload", {
    method: "POST",
    body: JSON.stringify({ filename: file.name, contentType: file.type }),
  }).then((r) => r.json());

  // Step 2: Upload directly to S3/R2
  await fetch(uploadUrl, {
    method: "PUT",
    body: file,
    headers: { "Content-Type": file.type },
  });

  return key; // Store this in your database
}
```

## Cloudflare R2 — S3-Compatible, No Egress Fees

```typescript
// R2 uses the same S3 SDK
const s3 = new S3Client({
  region: "auto",
  endpoint: `https://${process.env.CF_ACCOUNT_ID}.r2.cloudflarestorage.com`,
  credentials: {
    accessKeyId: process.env.R2_ACCESS_KEY!,
    secretAccessKey: process.env.R2_SECRET_KEY!,
  },
});
```

**Why R2 over S3**: Zero egress fees. For apps serving lots of files (images, documents), this saves significant money.

## Common Mistakes

1. **Uploading through your server** — doubles bandwidth costs and adds latency. Upload directly from client to storage.
2. **No file type validation** — validate both client-side (UX) and server-side (security).
3. **No size limits** — set max file size in presigned URL and validate client-side.
4. **Predictable file paths** — use UUIDs in paths to prevent enumeration.
5. **Not setting CORS on the bucket** — direct uploads from browser require CORS.

## See Also

- `hub/api-route-patterns.md` — API route for upload handling
- `research/security-checklist.md` — File upload security
