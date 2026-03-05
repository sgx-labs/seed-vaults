# Devcontainer Troubleshooting

## Container Won't Build

**Symptom:** "Reopen in Container" fails with build errors.

**Fixes:**
- Ensure Docker Desktop or OrbStack is running
- Use "Rebuild and Reopen in Container" (not just "Reopen")
- Check Docker has enough disk space: `docker system df`
- Check the Dockerfile syntax in the build log

## Slow Builds

**Symptom:** Container build takes a long time every time.

**Fixes:**
- Use `--no-install-recommends` in apt-get
- Combine `apt-get update && apt-get install` in one RUN
- Clean up with `rm -rf /var/lib/apt/lists/*`
- Order Dockerfile layers from least to most changing

## Line Ending Issues

**Symptom:** Shell scripts fail with `\r: command not found`.

**Fixes:**
- Add `.gitattributes` with `* text=auto eol=lf`
- Run `git add --renormalize .` to fix existing files
- Use "Rebuild and Reopen in Container" after fixing

## CGO Build Failures

**Symptom:** Go build fails with `cgo: C compiler "gcc" not found`.

**Fixes:**
- Install `build-essential` and `gcc` in Dockerfile
- Set `CGO_ENABLED=1` in containerEnv
- For SQLite: also install `sqlite3` and `libsqlite3-dev`

## Permission Denied

**Symptom:** Can't write to files or run commands.

**Fixes:**
- Check the `remoteUser` is set to `vscode` (not root)
- Mounted directories inherit host permissions
- Run `chmod` on host if needed before mounting

## Extensions Not Loading

**Symptom:** Recommended extensions don't appear.

**Fixes:**
- Extensions sidebar > filter `@recommended` > Install All
- Check `customizations.vscode.extensions` in devcontainer.json
- Some extensions need a container rebuild to activate

## Port Forwarding Not Working

**Symptom:** Can't access forwarded ports from browser.

**Fixes:**
- Check `forwardPorts` in devcontainer.json
- Ensure the service binds to `0.0.0.0` (not `127.0.0.1`) inside the container
- Try manually forwarding: Cmd+Shift+P > "Ports: Forward a Port"
