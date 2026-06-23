# moss-langgraph

Cryptographic signing for LangGraph stateful AI workflows using ML-DSA-44 post-quantum signatures.

[![PyPI](https://img.shields.io/pypi/v/moss-langgraph)](https://pypi.org/project/moss-langgraph/)
[![License](https://img.shields.io/badge/license-BSL--1.1-blue)](LICENSE)

## Overview

moss-langgraph integrates MOSS cryptographic signing into your LangGraph workflows. Every node output, checkpoint, and state transition gets a tamper-evident signature using ML-DSA-44 (NIST FIPS 204), the post-quantum cryptographic standard. This creates an immutable audit trail for compliance, debugging, and stateful workflow accountability.

## Installation

```bash
pip install moss-langgraph
```

## Quick Start

```python
from moss_langgraph import sign_node_output, sign_checkpoint, sign_state_transition

# Sign a node output
state = {"messages": ["Hello"], "step": 1}
result = sign_node_output(state, agent_id="my-graph", node="processor")
print(f"Signed: {result.signature[:20]}...")

# Sign a checkpoint
checkpoint = {"thread_id": "t1", "values": state}
result = sign_checkpoint(checkpoint, agent_id="my-graph", checkpoint_id="cp_1")
```

## Features

- **ML-DSA-44 signatures** - Post-quantum cryptographic standard (NIST FIPS 204)
- **Node output signing** - Sign every node's output state
- **Checkpoint signing** - Sign LangGraph checkpoints for state integrity
- **State transition signing** - Sign transitions between nodes
- **Policy enforcement** - Block high-risk actions with enterprise policies
- **Graph tracing** - Debug complex workflows with signed audit trail
- **Offline verification** - Verify signatures without network access

## Usage Examples

### Basic Usage

```python
from moss_langgraph import sign_node_output, sign_state_transition, verify_envelope

# Sign a node output
result = sign_node_output(
    {"action": "processed", "data": [1, 2, 3]},
    agent_id="my-graph",
    node="processor"
)

# Sign a state transition
result = sign_state_transition(
    from_state={"step": 1},
    to_state={"step": 2},
    agent_id="my-graph",
    from_node="step1",
    to_node="step2"
)

# Verify any envelope
verify_result = verify_envelope(result.envelope)
print(f"Valid: {verify_result.valid}, Subject: {verify_result.subject}")
```

### With Policy Enforcement

```python
import os
os.environ["MOSS_API_KEY"] = "your-api-key"

from moss_langgraph import sign_node_output

result = sign_node_output(
    {"action": "high_value_trade"},
    agent_id="trading-graph",
    node="executor",
    context={"user_id": "u123"}
)

if result.blocked:
    print(f"Blocked by policy: {result.policy.reason}")
```

## API Reference

| Function | Description |
|----------|-------------|
| `sign_node_output()` | Sign a node's output state |
| `sign_node_output_async()` | Async version |
| `sign_checkpoint()` | Sign a LangGraph checkpoint |
| `sign_checkpoint_async()` | Async version |
| `sign_state_transition()` | Sign a state transition |
| `sign_state_transition_async()` | Async version |
| `verify_envelope()` | Verify a signed envelope |
| `enterprise_enabled()` | Check if enterprise mode is active |

## Configuration

| Environment Variable | Description |
|---------------------|-------------|
| `MOSS_API_KEY` | API key for enterprise features (policy enforcement, SIEM) |
| `MOSS_API_URL` | Custom API endpoint (default: api.mosscomputing.com) |

## Links

- [Documentation](https://docs.mosscomputing.com/sdks/langgraph)
- [Dashboard](https://app.mosscomputing.com)
- [PyPI](https://pypi.org/project/moss-langgraph/)

## License

Business Source License 1.1 - Production use requires a [MOSS subscription](https://mosscomputing.com/pricing).
