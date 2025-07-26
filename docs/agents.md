# Agents

## Overview

Agents are stateful microservices in NodeCaster that perform autonomous tasks and interact through Kafka. Each agent maintains its own lifecycle and reacts to external decisions, allowing for modular design and parallelized orchestration.

---

## Agent Lifecycle

Agents follow a defined operational loop:

1. Gather internal status and environment data
2. Emit current state to the `agent-state` Kafka topic
3. Await decision from AI Engine or strategy plugin
4. Apply received decision and update internal state

This loop can run continuously or be event-driven.

---

## Internal Structure

Agents are implemented using Java or Kotlin and typically include:

- State manager: tracks position, context, and flags
- Kafka client: for publishing and consuming messages
- Action executor: applies decisions to internal state
- Config loader: loads agent-specific configuration

Each module is testable and can be mocked for simulation.

---

## State Payload Format

Agents emit JSON payloads like:

```json
{
  "agentId": "scout-42",
  "agentType": "Scout",
  "position": { "x": 12, "y": 7 },
  "context": { "elevation": 220, "obstacle": false },
  "timestamp": "2025-07-25T11:32:00Z"
}
```

Payload structure may vary by agent type.

---

## Decision Consumption

Agents subscribe to the `strategy-decisions` topic and expect decisions structured as:

```json
{
  "agentId": "scout-42",
  "action": "MOVE_NORTH",
  "metadata": {
    "priority": "HIGH",
    "source": "RiskAdjustedStrategy"
  }
}
```

Agents validate the decision and route it to the executor.

---

## Customization & Extension

Agents are customizable via:

- Agent-specific config files
- External annotations (for dynamic strategy assignment)
- Environment variable overrides

New agent types can be added by extending a shared `BaseAgent` class and implementing lifecycle hooks.

---

## Monitoring & Logging

Agents log:

- Lifecycle transitions
- Decision application outcomes
- Kafka message flow

Optional integration with Prometheus or OpenTelemetry enables system-wide observability.

---

## Example Agent Types

| Agent Type  | Role                                    |
|-------------|-----------------------------------------|
| `Scout`     | Explores terrain and relays context     |
| `Trader`    | Processes market signals and risks      |
| `Carrier`   | Coordinates with other agents to move payloads |
| `Observer`  | Monitors global state for feedback loops |

---

## Future Additions

- Add agent-level health checks via sidecars
- Support for rollback actions upon decision failure
- Agent collaboration via peer-to-peer messaging

NodeCaster agents remain intentionally flexible and language-agnostic within JVM boundaries.
