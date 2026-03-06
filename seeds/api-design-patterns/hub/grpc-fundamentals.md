---
title: "gRPC Fundamentals"
tags: [grpc, protobuf, streaming, rpc, microservices, performance, protocol-buffers]
content_type: hub
domain: api-design
---

# gRPC Fundamentals

## Overview

gRPC is a high-performance RPC framework using Protocol Buffers (protobuf) for serialization and HTTP/2 for transport. It generates client and server code from a shared schema definition. Primarily used for internal microservice communication where performance and type safety matter more than browser compatibility.

## Protocol Buffers (Protobuf)

The schema language for gRPC. Defines services, methods, and message types.

```protobuf
syntax = "proto3";

package users;

service UserService {
  rpc GetUser (GetUserRequest) returns (User);
  rpc ListUsers (ListUsersRequest) returns (ListUsersResponse);
  rpc CreateUser (CreateUserRequest) returns (User);
  rpc StreamUpdates (StreamRequest) returns (stream UserUpdate);
}

message User {
  string id = 1;
  string name = 2;
  string email = 3;
  int64 created_at = 4;
}

message GetUserRequest {
  string id = 1;
}

message ListUsersRequest {
  int32 limit = 1;
  string cursor = 2;
}

message ListUsersResponse {
  repeated User users = 1;
  string next_cursor = 2;
}
```

## Streaming Modes

gRPC supports four communication patterns:

| Pattern | Client | Server | Use Case |
|---------|--------|--------|----------|
| Unary | 1 request | 1 response | Standard request/response |
| Server streaming | 1 request | N responses | Live feeds, large result sets |
| Client streaming | N requests | 1 response | File upload, batch processing |
| Bidirectional | N requests | N responses | Chat, real-time collaboration |

```protobuf
// Unary
rpc GetUser (GetUserRequest) returns (User);

// Server streaming
rpc ListUsers (ListUsersRequest) returns (stream User);

// Client streaming
rpc UploadChunks (stream Chunk) returns (UploadResult);

// Bidirectional
rpc Chat (stream Message) returns (stream Message);
```

## When to Use gRPC

**Good fit**: Microservice-to-microservice communication, performance-critical internal APIs, polyglot environments (auto-generated clients in any language), streaming use cases, mobile apps with bandwidth constraints (protobuf is much smaller than JSON).

**Poor fit**: Browser clients (limited native browser support, needs grpc-web proxy), public APIs for external developers (REST/JSON is more accessible), simple CRUD operations (overkill), teams unfamiliar with protobuf.

## When NOT to Use gRPC

- **Your API is browser-facing** -- use REST or GraphQL. grpc-web exists but adds complexity.
- **Your API is consumed by external developers** -- JSON is universal. Protobuf requires tooling.
- **You have a simple API** -- the proto compilation step, code generation, and tooling are overhead you don't need for 5 endpoints.
- **You need human-readable payloads** -- protobuf is binary. You can't curl a gRPC endpoint and read the response.

## gRPC vs REST

| Factor | gRPC | REST |
|--------|------|------|
| Payload format | Protobuf (binary) | JSON (text) |
| Transport | HTTP/2 | HTTP/1.1 or HTTP/2 |
| Schema | Required (.proto) | Optional (OpenAPI) |
| Code generation | Built-in | Optional |
| Browser support | Limited (grpc-web) | Native |
| Streaming | Built-in (4 patterns) | SSE, WebSocket (separate) |
| Performance | Faster (binary, HTTP/2) | Slower (JSON parsing, text) |
| Tooling | protoc, plugins | curl, Postman, any HTTP client |

## Best Practices

1. **Use proto3** -- proto2 is legacy. Proto3 has sensible defaults.
2. **Never reuse field numbers** -- deleted fields should be reserved, not recycled.
3. **Version via package names** -- `package users.v1;` allows parallel versions.
4. **Use well-known types** -- `google.protobuf.Timestamp`, `google.protobuf.Duration` instead of raw integers.
5. **Implement health checks** -- use the gRPC health checking protocol for load balancers.
6. **Set deadlines** -- every RPC should have a timeout. Propagate deadlines across service calls.
7. **Use interceptors** -- middleware equivalent for logging, auth, metrics, tracing.

## See Also

- `entities/graphql-vs-rest.md` -- Broader comparison including gRPC
- `research/microservices-communication.md` -- Communication patterns between services
- `hub/api-gateway.md` -- Gateways that support gRPC
