# Devcontainer Security Hardening

## umask 077

Set restrictive default permissions so new files are only readable by the owner:

```dockerfile
RUN echo "umask 077" >> /etc/profile.d/dev-security.sh
```

## .gitignore Hardening

Always exclude sensitive directories and files:

```gitignore
.same/
.claude/
.env
.env.*
*.pid
*.sock
```

## Config Directory Mounts

Mount host config directories with bind mounts rather than copying:

```json
{
  "mounts": [
    "source=${localEnv:HOME}/.same,target=/home/vscode/.same,type=bind",
    "source=${localEnv:HOME}/.claude,target=/home/vscode/.claude,type=bind"
  ]
}
```

This ensures:
- Config persists across container rebuilds
- Permissions are inherited from the host
- Secrets (API keys, tokens) never get baked into the image

## Non-Root User

Always switch to a non-root user in the Dockerfile:

```dockerfile
USER vscode
WORKDIR /workspace
```

## Line Endings

Prevent CRLF issues with `.gitattributes`:

```
* text=auto eol=lf
*.sh text eol=lf
*.cmd text eol=crlf
*.bat text eol=crlf
```

## Workspace Trust

Enable VS Code workspace trust:

```json
{
  "security.workspace.trust.enabled": true
}
```
