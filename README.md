# agentanycast-proto

Protocol Buffer definitions for the AgentAnycast ecosystem.

This repository is the **single source of truth** for all cross-component interfaces:

- **SDK ↔ Daemon gRPC interface** (`node_service.proto`)
- **A2A data models** (`a2a_models.proto`) — Task, Message, Artifact, Part, A2AEnvelope
- **Agent Card** (`agent_card.proto`) — A2A-compatible capability descriptor with P2P extensions
- **Common types** (`common.proto`) — PeerInfo, NodeInfo, enums

## License

Apache 2.0
