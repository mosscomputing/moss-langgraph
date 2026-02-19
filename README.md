# moss-langgraph

MOSS signing integration for LangGraph. **Unsigned output is broken output.**

[![PyPI](https://img.shields.io/pypi/v/moss-langgraph)](https://pypi.org/project/moss-langgraph/)

## Installation

```bash
pip install moss-langgraph
```

## Quick Start: Explicit Signing (Recommended)

Sign node outputs, checkpoints, and state transitions:

```python
from moss_langgraph import sign_node_output, sign_checkpoint, sign_state_transition

# Sign a node output
state = {"messages": ["Hello"], "step": 1}
result = sign_node_output(state, agent_id="my-graph", node="processor")
print(f"Signed: {result.signature[:20]}...")

# Sign a checkpoint
checkpoint = {"thread_id": "t1", "values": state}
result = sign_checkpoint(checkpoint, agent_id="my-graph", checkpoint_id="cp_1")

# Sign a state transition
result = sign_state_transition(
    from_state={"step": 1},
    to_state={"step": 2},
    agent_id="my-graph",
    from_node="step1",
    to_node="step2"
)
```

## Enterprise Mode

Set `MOSS_API_KEY` for automatic policy evaluation:

```python
import os
os.environ["MOSS_API_KEY"] = "your-api-key"

from moss_langgraph import sign_node_output, enterprise_enabled

print(f"Enterprise: {enterprise_enabled()}")  # True

result = sign_node_output(
    {"action": "high_value_trade"},
    agent_id="trading-graph",
    node="executor",
    context={"user_id": "u123"}
)

if result.blocked:
    print(f"Blocked by policy: {result.policy.reason}")
```

## Verification

```python
from moss_langgraph import verify_envelope

verify_result = verify_envelope(result.envelope)
if verify_result.valid:
    print(f"Signed by: {verify_result.subject}")
```

## All Functions

| Function | Description |
|----------|-------------|
| `sign_node_output()` | Sign a node's output state |
| `sign_node_output_async()` | Async version |
| `sign_checkpoint()` | Sign a LangGraph checkpoint |
| `sign_checkpoint_async()` | Async version |
| `sign_state_transition()` | Sign a state transition |
| `sign_state_transition_async()` | Async version |
| `verify_envelope()` | Verify a signed envelope |

## Legacy API: Auto-Signing Decorator

The old decorator API is still available:

```python
from langgraph.graph import StateGraph
from moss_langgraph import signed_node, SignedNodeFactory

# Wrap individual nodes
graph.add_node("step", signed_node(my_node, "moss:flow:step"))

# Or use factory for multiple nodes
factory = SignedNodeFactory("moss:flow:pipeline")
graph.add_node("step1", factory.wrap(step1_fn))
graph.add_node("step2", factory.wrap(step2_fn))
```

## Pricing Tiers

| Tier | Price | Agents | Signatures | Retention |
|------|-------|--------|------------|-----------|
| **Free** | $0 | 5 | 1,000/day | 7 days |
| **Pro** | $1,499/mo | Unlimited | Unlimited | 1 year |
| **Enterprise** | Custom | Unlimited | Unlimited | 7 years |

*Annual billing: $1,249/mo (save $3,000/year)*

All new signups get a **14-day free trial** of Pro.

### Features by Tier

| Feature | Free | Pro | Enterprise |
|---------|------|-----|------------|
| Local signing | ✓ | ✓ | ✓ |
| Offline verification | ✓ | ✓ | ✓ |
| Policy evaluation | - | ✓ | ✓ |
| RBAC | - | ✓ | ✓ |
| Evidence retention | 7 days | 1 year | 7 years |
| Slack/Teams alerts | - | ✓ | ✓ + Buttons |
| SIEM integration | - | ✓ | ✓ |
| Compliance exports | - | ✓ | ✓ |

## Links

- [moss-sdk](https://pypi.org/project/moss-sdk/) - Core MOSS SDK
- [mosscomputing.com](https://mosscomputing.com) - Project site

## License

This package is licensed under the [Business Source License 1.1](LICENSE).

- Free for evaluation, testing, and development
- Free for non-production use
- Production use requires a [MOSS subscription](https://mosscomputing.com/pricing)
- Converts to Apache 2.0 on January 25, 2030
