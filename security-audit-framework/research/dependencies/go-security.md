---
title: "Go Module Security"
tags: [dependencies, go, modules, govulncheck, go-sum, goprivate, sql-injection]
content_type: research
domain: security
---

# Go Module Security

Go has strong built-in supply chain protection through its module system, proxy, and checksum database. However, you still need to actively audit.

## Go Module Security Features

| Feature | What It Does | Enabled By Default |
|---------|-------------|-------------------|
| Go proxy (proxy.golang.org) | Caches and serves modules | Yes |
| Checksum database (sum.golang.org) | Verifies module integrity | Yes |
| go.sum | Local checksum verification | Yes (when present) |
| govulncheck | Vulnerability scanning | No (must install) |

## Essential Commands

```bash
# Check for known vulnerabilities (only reports reachable code)
go install golang.org/x/vuln/cmd/govulncheck@latest
govulncheck ./...

# Verify module checksums
go mod verify

# Clean up unused dependencies
go mod tidy

# View dependency graph
go mod graph

# Check for outdated modules
go list -m -u all
```

## govulncheck in CI

```yaml
# GitHub Actions
- name: Install govulncheck
  run: go install golang.org/x/vuln/cmd/govulncheck@latest

- name: Run govulncheck
  run: govulncheck ./...
```

govulncheck is smarter than most SCA tools: it only reports vulnerabilities in code paths your application actually calls, reducing false positives significantly.

## Go-Specific Security Patterns

### Secure HTTP Client

```go
// SECURE — HTTP client with timeouts and TLS verification
client := &http.Client{
    Timeout: 30 * time.Second,
    Transport: &http.Transport{
        TLSClientConfig: &tls.Config{
            MinVersion: tls.VersionTLS12,
            // Do NOT set InsecureSkipVerify: true
        },
        MaxIdleConns:        100,
        IdleConnTimeout:     90 * time.Second,
        DisableCompression:  false,
    },
}
```

### Secure SQL Queries

```go
// SECURE — Always use parameterized queries
row := db.QueryRow("SELECT name, email FROM users WHERE id = $1", userID)

// VULNERABLE — String formatting in queries
// query := fmt.Sprintf("SELECT * FROM users WHERE id = '%s'", userID)
```

### Error Handling

```go
// SECURE — Never expose internal errors to clients
func handler(w http.ResponseWriter, r *http.Request) {
    result, err := db.Query(r.URL.Query().Get("id"))
    if err != nil {
        // Log internally with detail
        log.Printf("database error: %v", err)
        // Generic error to client
        http.Error(w, "Internal server error", http.StatusInternalServerError)
        return
    }
    // ...
}
```

## Private Module Security

```bash
# Set GOPRIVATE for internal modules (skip proxy and checksum DB)
export GOPRIVATE=github.com/your-org/*

# Use GONOSUMDB for specific private modules only
export GONOSUMDB=github.com/your-org/*

# Never set GONOSUMCHECK globally — it disables integrity verification
```

## How to Test

1. Run `govulncheck ./...` on your project
2. Run `go mod verify` to check checksums
3. Search for `InsecureSkipVerify: true` in the codebase
4. Check for string concatenation in SQL queries
5. Verify GOPRIVATE is set correctly (not too broad)

## See Also

- hub/dependencies.md
- research/dependencies/vulnerable-components.md
- research/dependencies/ci-cd-scanning.md
