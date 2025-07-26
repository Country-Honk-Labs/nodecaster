# System Events Module

## Overview

System events represent coordination messages and operational signals used across the NodeCaster framework. These events orchestrate agent behavior, update strategy execution, and manage distributed state.

They are typically lightweight, domain-agnostic, and published over Kafka topics with clear routing semantics.

---

## Event Categories

- **Control Signals**: Start/stop commands, reboots, failover instructions.
- **Overrides**: Temporary rule bypasses, configuration adjustments.
- **Heartbeat**: Periodic pings from agents or services.
- **Status Updates**: Agent health, plugin success/failure, environment syncs.
- **Broadcasts**: System-wide announcements or consensus results.

---

## Kafka Topics

| Topic Name             | Description                          |
|------------------------|--------------------------------------|
| `system-control`       | Core coordination commands           |
| `system-heartbeat`     | Agent/service keepalive pulses       |
| `system-status`        | Runtime status and health signals    |
| `system-broadcast`     | Shared announcements and global data |
| `system-override`      | Rule or behavior adjustment messages |

Consumers and producers should adhere to schema versions for compatibility.

---

## Example Events

### Heartbeat

```json
{
  "agentId": "node-caster-03",
  "timestamp": "2025-07-26T00:10:00Z",
  "status": "healthy"
}
```

### Override

```json
{
  "target": "strategy-alpha",
  "timestamp": "2025-07-26T00:12:00Z",
  "override": {
    "type": "threshold-adjustment",
    "newValue": 0.85,
    "durationSeconds": 120
  }
}
```

---

## Consumption Model

- **Agents**: Listen for broadcasts and overrides to update local behavior.
- **Decision Engine**: Uses control and status events to tune scoring and validation.
- **Supervisors**: Can emit and validate overrides based on performance or drift.

Event messages are typically JSON, validated with Avro schemas when required.

---

## Coordination Patterns

- Leader-election algorithms may emit `system-control` events for role changes.
- Agents register themselves via `system-status` and emit heartbeats.
- Overrides are time-bound or conditionally revoked via supervisor feedback.

---

## Reliability & Ordering

- Kafka partitioning ensures reliable delivery and ordering per agent/service.
- Consumers must handle deduplication and reprocessing gracefully.
- Events may include metadata for causal linking (e.g., `eventChainId`).

---

## Future Extensions

- Signed override requests for audit and security
- Cluster-wide health dashboards using system event streams
- Coordination with external schedulers (e.g., Nomad, Airflow)
