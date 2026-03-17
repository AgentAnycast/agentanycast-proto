# AgentAnycast Proto

[![Buf Lint](https://github.com/AgentAnycast/agentanycast-proto/actions/workflows/ci.yml/badge.svg)](https://github.com/AgentAnycast/agentanycast-proto/actions)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue)](LICENSE)

Protocol Buffer definitions for the [AgentAnycast](https://github.com/AgentAnycast/agentanycast) ecosystem — the **single source of truth** for all cross-component interfaces.

## Proto Files

| File | Description |
|---|---|
| `node_service.proto` | gRPC service (13 RPCs) between the Python SDK and Go daemon |
| `a2a_models.proto` | A2A data models — Task, Message, Artifact, Part, A2AEnvelope |
| `agent_card.proto` | Agent Card — A2A-compatible capability descriptor with P2P extensions |
| `common.proto` | Shared types — PeerInfo, NodeInfo, TaskStatus enum |

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

## Conventions

- **Package:** `agentanycast.v1`
- **Lint rules:** Buf STANDARD (enforced in CI)
- **Breaking change policy:** FILE-level detection — never remove or rename fields, only additive changes
- **Go package:** `github.com/agentanycast/agentanycast-proto/gen/go/agentanycast/v1`

## Dependency Flow

```
agentanycast-proto (this repo)
├── → agentanycast-node   (Go: local replace in go.mod)
└── → agentanycast-python (Python: vendored stubs in _generated/)
```

## License

[Apache License, Version 2.0](LICENSE)
