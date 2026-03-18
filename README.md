# AgentAnycast Proto

[![Buf Lint](https://github.com/AgentAnycast/agentanycast-proto/actions/workflows/ci.yml/badge.svg)](https://github.com/AgentAnycast/agentanycast-proto/actions)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue)](LICENSE)

Protocol Buffer definitions for the [AgentAnycast](https://github.com/AgentAnycast/agentanycast) ecosystem — the **single source of truth** for all cross-component interfaces.

## Proto Files

| File | Description |
|---|---|
| `node_service.proto` | gRPC `NodeService` (16 RPCs) between the Python SDK and Go daemon |
| `a2a_models.proto` | A2A data models — Task, Message, Artifact, Part, A2AEnvelope (11 envelope types) |
| `agent_card.proto` | Agent Card — A2A-compatible capability descriptor with P2P extension |
| `common.proto` | Shared types — PeerInfo, NodeInfo, TaskStatus enum |
| `streaming.proto` | Streaming artifact delivery — StreamStart, StreamChunk, StreamEnd |
| `registry_service.proto` | Skill Registry service (4 RPCs) hosted on the Relay server |

## Usage

### Prerequisites

Install [Buf CLI](https://buf.build/docs/installation):

```bash
# macOS
brew install bufbuild/buf/buf

# Other platforms: https://buf.build/docs/installation
```

### Generate Code

```bash
buf generate
```

This produces:

```
gen/
├── go/agentanycast/v1/       # Go stubs (used by agentanycast-node)
│   ├── *_pb.go
│   └── *_grpc.go
└── python/agentanycast/v1/   # Python stubs (used by agentanycast-python)
    ├── *_pb2.py
    └── *_pb2_grpc.py
```

> **Note:** `gen/` is gitignored. Downstream repos either run `buf generate` in CI or vendor the stubs.

### Lint & Breaking Change Detection

```bash
buf lint                                    # Lint against STANDARD rules
buf breaking --against '.git#branch=main'   # Detect breaking changes vs main
```

## Service Overview

### NodeService (16 RPCs)

The main gRPC service used by SDKs to control the daemon:

| Group | RPCs |
|---|---|
| **Node** | `GetNodeInfo`, `SetAgentCard` |
| **Peers** | `ConnectPeer`, `ListPeers`, `GetPeerCard` |
| **Task Client** | `SendTask`, `GetTask`, `CancelTask`, `SubscribeTaskUpdates` |
| **Task Server** | `SubscribeIncomingTasks`, `UpdateTaskStatus`, `CompleteTask`, `FailTask` |
| **Streaming** | `SubscribeTaskStream`, `SendStreamingArtifact` |
| **Discovery** | `Discover` |

`SendTask` supports three addressing modes via a `oneof target`:

| Field | Mode | Description |
|---|---|---|
| `peer_id` | Direct | Send to a specific Peer ID |
| `skill_id` | Anycast | Route by skill via registry/DHT |
| `url` | HTTP Bridge | Call an external HTTP A2A agent |

### RegistryService (4 RPCs)

Hosted on the Relay server for skill-based agent discovery:

| RPC | Description |
|---|---|
| `RegisterSkills` | Register skills with metadata and tags |
| `UnregisterSkills` | Remove specific skill registrations |
| `DiscoverBySkill` | Find agents by skill ID with optional tag filtering |
| `Heartbeat` | Renew TTL on existing registrations |

### A2A Envelope Types (11)

| Type | Direction | Purpose |
|---|---|---|
| `SEND_TASK` | Client → Server | Initiate a new task |
| `TASK_STATUS_UPDATE` | Server → Client | Report progress |
| `TASK_COMPLETE` | Server → Client | Deliver results |
| `TASK_FAIL` | Server → Client | Report failure |
| `TASK_CANCEL` | Client → Server | Request cancellation |
| `GET_TASK` | Client → Server | Query task state |
| `GET_TASK_RESPONSE` | Server → Client | Return task state |
| `ACK` | Bidirectional | Acknowledge receipt |
| `STREAM_START` | Server → Client | Begin streaming artifact |
| `STREAM_CHUNK` | Server → Client | Deliver artifact chunk |
| `STREAM_END` | Server → Client | End streaming |

## Conventions

- **Package:** `agentanycast.v1`
- **Lint rules:** Buf STANDARD (enforced in CI)
- **Breaking change policy:** FILE-level detection — never remove or rename fields, only additive changes
- **Go package:** `github.com/agentanycast/agentanycast-proto/gen/go/agentanycast/v1`

## Dependency Flow

```
agentanycast-proto (this repo)
├── → agentanycast-node   (Go: local replace in go.mod)
├── → agentanycast-relay  (Go: local replace in go.mod)
└── → agentanycast-python (Python: vendored stubs in _generated/)
```

## License

[Apache License, Version 2.0](LICENSE)
