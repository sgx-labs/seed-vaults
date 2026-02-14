---
title: "Path Traversal Prevention"
tags: [owasp, path-traversal, directory-traversal, file-access]
content_type: research
domain: security
---

# Path Traversal Prevention

Path traversal (directory traversal) attacks use `../` sequences to access files outside the intended directory. This can expose source code, configuration files, and system files.

## How It Works

An application serves files based on user input:
```
GET /files?name=report.pdf     -> /var/app/uploads/report.pdf
GET /files?name=../../etc/passwd -> /var/app/uploads/../../etc/passwd -> /etc/passwd
```

## Vulnerable: Direct Path Construction — DO NOT USE

```javascript
// VULNERABLE — User input directly in file path
const path = require('path');
const fs = require('fs');

app.get('/files', (req, res) => {
  const filePath = path.join('/var/app/uploads', req.query.name);
  res.sendFile(filePath);
  // Attack: name=../../etc/passwd -> reads system files
});
```

## Secure: Path Validation

```javascript
// SECURE — Validate resolved path stays within allowed directory
const path = require('path');

const UPLOAD_DIR = '/var/app/uploads';

app.get('/files', (req, res) => {
  const fileName = req.query.name;

  // Reject obvious traversal attempts
  if (!fileName || fileName.includes('..') || fileName.includes('\0')) {
    return res.status(400).json({ error: 'Invalid filename' });
  }

  // Resolve and verify the path stays within allowed directory
  const resolvedPath = path.resolve(UPLOAD_DIR, fileName);
  if (!resolvedPath.startsWith(path.resolve(UPLOAD_DIR) + path.sep)) {
    return res.status(403).json({ error: 'Access denied' });
  }

  // Verify file exists before serving
  if (!fs.existsSync(resolvedPath)) {
    return res.status(404).json({ error: 'File not found' });
  }

  res.sendFile(resolvedPath);
});
```

## Secure: Python Path Validation

```python
# SECURE — Python pathlib for safe path resolution
from pathlib import Path

UPLOAD_DIR = Path('/var/app/uploads').resolve()

@app.route('/files')
def serve_file():
    filename = request.args.get('name', '')

    if not filename or '..' in filename:
        abort(400, 'Invalid filename')

    file_path = (UPLOAD_DIR / filename).resolve()

    # Verify path is within allowed directory
    if not str(file_path).startswith(str(UPLOAD_DIR)):
        abort(403, 'Access denied')

    if not file_path.is_file():
        abort(404, 'File not found')

    return send_file(file_path)
```

## Secure: Go Path Validation

```go
// SECURE — Go filepath.Clean and prefix check
func serveFile(w http.ResponseWriter, r *http.Request) {
    uploadDir := "/var/app/uploads"
    name := r.URL.Query().Get("name")

    if name == "" || strings.Contains(name, "..") {
        http.Error(w, "Invalid filename", http.StatusBadRequest)
        return
    }

    cleanPath := filepath.Clean(filepath.Join(uploadDir, name))
    if !strings.HasPrefix(cleanPath, filepath.Clean(uploadDir)+string(os.PathSeparator)) {
        http.Error(w, "Access denied", http.StatusForbidden)
        return
    }

    http.ServeFile(w, r, cleanPath)
}
```

## Additional Defenses

1. **Use a database mapping** — Store files by ID, not user-provided names
2. **Use a CDN or object storage** — S3/GCS with signed URLs
3. **Chroot/sandbox** — Run the application in a restricted filesystem
4. **Generate filenames** — Use UUIDs instead of user-provided names

## How to Test

1. Try `../` in file path parameters
2. Try URL-encoded traversal: `%2e%2e%2f`
3. Try double-encoding: `%252e%252e%252f`
4. Try null bytes: `file.txt%00.jpg`
5. Use automated tools: Semgrep, OWASP ZAP

## See Also

- hub/owasp-top-10.md — A01: Broken Access Control
- research/api-security/file-uploads.md
- research/api-security/input-validation.md
