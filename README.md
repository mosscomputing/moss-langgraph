# moss-langgraph

MOSS signing integration for LangGraph nodes. **Unsigned output is broken output.**

## Installation

```bash
pip install moss-langgraph
```

## Usage

```python
from langgraph.graph import StateGraph
from moss_langgraph import signed_node

def my_node(state):
    state["result"] = "computed"
    return state

# Create graph and add signed node
graph = StateGraph(dict)
graph.add_node("step", signed_node(my_node, "moss:flow:step"))

# After node executes, state["moss_envelope"] is populated
# envelope.signature: ML-DSA-44 post-quantum signature
# envelope.timestamp: Signed timestamp
# envelope.verify(): Returns VerifyResult
```

## Verification

```python
from moss import verify

# After graph execution
result = graph.invoke({"input": "data"})
envelope = result["moss_envelope"]

# Verify the node's output - no network required
verify_result = verify(envelope)

if verify_result.valid:
    print(f"Signed by: {verify_result.subject}")
else:
    print(f"Invalid: {verify_result.reason}")

# Or use envelope.verify() directly
assert envelope.verify().valid
```

## Factory for Multiple Nodes

```python
from moss_langgraph import SignedNodeFactory

# All nodes share the same subject
factory = SignedNodeFactory("moss:flow:pipeline")

graph.add_node("step1", factory.wrap(step1_fn))
graph.add_node("step2", factory.wrap(step2_fn))
graph.add_node("step3", factory.wrap(step3_fn))
```

## Different Subjects per Node

```python
graph.add_node("research", signed_node(research_fn, "moss:flow:research"))
graph.add_node("analyze", signed_node(analyze_fn, "moss:flow:analyze"))
graph.add_node("summarize", signed_node(summarize_fn, "moss:flow:summarize"))
```
