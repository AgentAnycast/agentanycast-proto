# Contributing

Thank you for your interest in contributing to AgentAnycast!

Please see the [Contributing Guide](https://github.com/AgentAnycast/agentanycast/blob/main/CONTRIBUTING.md) in the main repository for guidelines on:

- Contribution workflow
- Coding standards
- Commit message conventions
- Cross-repository changes
- DCO sign-off requirements

## Proto-Specific Guidelines

- All changes must pass `buf lint` (STANDARD rules)
- Never remove or rename existing fields — only additive changes
- Run `buf breaking --against '.git#branch=main'` before submitting
- Update downstream repos (agentanycast-node, agentanycast-python) if adding new RPCs
