---
title: "Secure File Upload Handling"
tags: [api-security, file-upload, validation, malware, mime-type, path-traversal, multer]
content_type: research
domain: security
---

# Secure File Upload Handling

File uploads are a high-risk feature. Without proper validation, they enable remote code execution, path traversal, denial of service, and malware distribution.

## Attack Vectors

- **Executable upload** — .php, .jsp, .sh files executed by the server
- **Path traversal** — Filename like `../../etc/passwd` writes to wrong location
- **MIME type spoofing** — File claims to be image but contains script
- **Zip bomb** — Small compressed file expands to fill disk
- **XSS via SVG** — SVG files containing embedded JavaScript

## Vulnerable: No Validation — DO NOT USE

```javascript
// VULNERABLE — Accepts any file, uses original filename
const multer = require('multer');
const upload = multer({ dest: 'public/uploads/' });

app.post('/upload', upload.single('file'), (req, res) => {
  res.json({ path: `/uploads/${req.file.originalname}` });
});
```

## Secure: Full Validation

```javascript
// SECURE — Validated file upload
const multer = require('multer');
const crypto = require('crypto');
const path = require('path');

const ALLOWED_MIMES = ['image/jpeg', 'image/png', 'image/webp', 'application/pdf'];
const MAX_SIZE = 10 * 1024 * 1024; // 10 MB

const storage = multer.diskStorage({
  destination: '/var/app/uploads/', // Outside web root
  filename: (req, file, cb) => {
    // Generate random filename — never use original
    const ext = path.extname(file.originalname).toLowerCase();
    const name = crypto.randomBytes(16).toString('hex');
    cb(null, `${name}${ext}`);
  },
});

const upload = multer({
  storage,
  limits: {
    fileSize: MAX_SIZE,
    files: 1,
  },
  fileFilter: (req, file, cb) => {
    if (!ALLOWED_MIMES.includes(file.mimetype)) {
      return cb(new Error('Invalid file type'));
    }
    const ext = path.extname(file.originalname).toLowerCase();
    const ALLOWED_EXTS = ['.jpg', '.jpeg', '.png', '.webp', '.pdf'];
    if (!ALLOWED_EXTS.includes(ext)) {
      return cb(new Error('Invalid file extension'));
    }
    cb(null, true);
  },
});

app.post('/upload', authenticate, upload.single('file'), async (req, res) => {
  // Additional: verify file magic bytes match claimed type
  const fileType = await FileType.fromFile(req.file.path);
  if (!fileType || !ALLOWED_MIMES.includes(fileType.mime)) {
    fs.unlinkSync(req.file.path); // Delete suspicious file
    return res.status(400).json({ error: 'File content does not match type' });
  }

  // Store file reference in database, serve via controlled endpoint
  await db.storeFile(req.user.id, req.file.filename, fileType.mime);
  res.json({ fileId: req.file.filename });
});
```

## Python/Flask Secure Upload

```python
# SECURE — Flask file upload with validation
import os
import uuid
from werkzeug.utils import secure_filename

ALLOWED_EXTENSIONS = {'png', 'jpg', 'jpeg', 'pdf'}
MAX_SIZE = 10 * 1024 * 1024  # 10 MB
UPLOAD_DIR = '/var/app/uploads'  # Outside web root

def allowed_file(filename):
    return '.' in filename and \
        filename.rsplit('.', 1)[1].lower() in ALLOWED_EXTENSIONS

@app.route('/upload', methods=['POST'])
@login_required
def upload_file():
    if 'file' not in request.files:
        return jsonify({"error": "No file provided"}), 400

    file = request.files['file']
    if not file.filename or not allowed_file(file.filename):
        return jsonify({"error": "Invalid file type"}), 400

    # Check size
    file.seek(0, os.SEEK_END)
    if file.tell() > MAX_SIZE:
        return jsonify({"error": "File too large"}), 400
    file.seek(0)

    # Generate safe filename
    ext = file.filename.rsplit('.', 1)[1].lower()
    safe_name = f"{uuid.uuid4().hex}.{ext}"

    file.save(os.path.join(UPLOAD_DIR, safe_name))
    return jsonify({"fileId": safe_name})
```

## Serving Uploaded Files

```javascript
// SECURE — Serve files with proper headers, no direct path access
app.get('/files/:fileId', authenticate, async (req, res) => {
  const fileRecord = await db.getFile(req.params.fileId);
  if (!fileRecord || fileRecord.userId !== req.user.id) {
    return res.status(404).json({ error: 'Not found' });
  }
  res.setHeader('Content-Type', fileRecord.mimeType);
  res.setHeader('Content-Disposition', 'attachment'); // Force download
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.sendFile(path.join('/var/app/uploads', fileRecord.filename));
});
```

## Checklist

- [ ] Store uploads OUTSIDE the web root
- [ ] Generate random filenames (never use originals)
- [ ] Validate MIME type AND extension AND magic bytes
- [ ] Set file size limits
- [ ] Scan for malware (ClamAV or similar)
- [ ] Serve uploaded files with `Content-Disposition: attachment`
- [ ] Set `X-Content-Type-Options: nosniff` on file responses
- [ ] Block SVG uploads or sanitize them (XSS risk)

## See Also

- hub/api-security.md
- research/api-security/input-validation.md
- research/owasp/security-misconfiguration.md
