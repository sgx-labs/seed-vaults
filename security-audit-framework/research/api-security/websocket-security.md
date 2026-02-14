---
title: "WebSocket Security"
tags: [api-security, websocket, real-time, authentication]
content_type: research
domain: security
---

# WebSocket Security

WebSockets bypass many traditional HTTP security mechanisms. They require explicit security measures that HTTP gets "for free."

## Key Risks

- WebSocket connections are not protected by CORS
- The initial HTTP handshake is the only authentication point
- No built-in rate limiting or message size limits
- Messages are not automatically encrypted (use wss://)
- Long-lived connections can be hijacked if not validated

## Vulnerable: No Authentication — DO NOT USE

```javascript
// VULNERABLE — Any browser tab can connect, no auth
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

wss.on('connection', (ws) => {
  ws.on('message', (data) => {
    // Broadcasting to all without authentication
    wss.clients.forEach(client => client.send(data));
  });
});
```

## Secure: Authenticated WebSocket

```javascript
// SECURE — Token-based auth on handshake + message validation
const WebSocket = require('ws');
const jwt = require('jsonwebtoken');

const wss = new WebSocket.Server({ noServer: true });

// Authenticate during HTTP upgrade
server.on('upgrade', (request, socket, head) => {
  // Extract token from query string or cookie
  const url = new URL(request.url, 'https://example.com');
  const token = url.searchParams.get('token');

  try {
    const user = jwt.verify(token, publicKey, {
      algorithms: ['RS256'],
      issuer: 'https://auth.example.com',
    });
    request.user = user;
    wss.handleUpgrade(request, socket, head, (ws) => {
      wss.emit('connection', ws, request);
    });
  } catch (err) {
    socket.write('HTTP/1.1 401 Unauthorized\r\n\r\n');
    socket.destroy();
  }
});

// Message handling with validation
wss.on('connection', (ws, request) => {
  const user = request.user;
  let messageCount = 0;
  const RATE_LIMIT = 30; // messages per second
  const MAX_MESSAGE_SIZE = 64 * 1024; // 64 KB

  ws.on('message', (data) => {
    // Rate limiting
    messageCount++;
    if (messageCount > RATE_LIMIT) {
      ws.close(1008, 'Rate limit exceeded');
      return;
    }

    // Size validation
    if (data.length > MAX_MESSAGE_SIZE) {
      ws.close(1009, 'Message too large');
      return;
    }

    // Parse and validate message content
    let message;
    try {
      message = JSON.parse(data);
    } catch (e) {
      ws.send(JSON.stringify({ error: 'Invalid JSON' }));
      return;
    }

    // Process validated message with user context
    handleMessage(user, message);
  });

  // Reset rate counter every second
  const rateLimitInterval = setInterval(() => { messageCount = 0; }, 1000);
  ws.on('close', () => clearInterval(rateLimitInterval));
});
```

## Origin Validation

```javascript
// SECURE — Verify the Origin header during upgrade
server.on('upgrade', (request, socket, head) => {
  const origin = request.headers.origin;
  const ALLOWED_ORIGINS = ['https://app.example.com'];

  if (!ALLOWED_ORIGINS.includes(origin)) {
    socket.write('HTTP/1.1 403 Forbidden\r\n\r\n');
    socket.destroy();
    return;
  }
  // Continue with authentication...
});
```

## Checklist

- [ ] Always use `wss://` (encrypted WebSocket) in production
- [ ] Authenticate during the HTTP upgrade handshake
- [ ] Validate the Origin header
- [ ] Implement message-level rate limiting
- [ ] Set maximum message size limits
- [ ] Validate and sanitize all incoming messages
- [ ] Implement heartbeat/ping-pong for stale connection detection
- [ ] Log connection events (connect, disconnect, errors)
- [ ] Handle reconnection gracefully (client-side)

## How to Test

1. Try connecting without authentication — should be rejected
2. Try connecting from a different origin — should be rejected
3. Send oversized messages — should be disconnected
4. Send rapid-fire messages — should be rate limited
5. Send malformed JSON — should get an error response

## See Also

- hub/api-security.md
- research/authentication/jwt-security.md
- research/api-security/rate-limiting.md
