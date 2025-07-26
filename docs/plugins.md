# Strategy Plugins

## Overview

Strategy plugins encapsulate decision-making logic that guides agent behavior in NodeCaster. These plugins are modular, hot-swappable, and adhere to a shared interface for seamless integration with the Decision Engine.

---

## Plugin Structure

Each plugin is a Java module that implements the `DecisionStrategy` interface:

```java
interface DecisionStrategy {
    AgentAction decide(AgentState state, EnvironmentData env);
}
```

Plugins can use:
- Heuristic logic
- Finite state machines
- Machine learning models (via external calls or embedded inference)

Plugin modules are packaged independently and loaded on demand.

---

## Integration

### Loading Plugins

Plugins are registered via:
- Classpath scanning (default)
- Dynamic class loading from JARs
- Remote configuration via Kafka or REST

At runtime, the Decision Engine maps each agent to a strategy using config files or environment variables.

### Plugin Activation

Strategies can be toggled through:
- Environment-based targeting
- Agent-specific annotations
- System-level overrides (from `system-events` Kafka topic)

This allows adaptive routing and experimentation without downtime.

---

## Configuration Options

```properties
strategy.default=BasicStrategy
strategy.agentType[Scout]=TerrainAwareStrategy
strategy.agentType[Trader]=RiskAdjustedStrategy
strategy.dynamic.enable=true
strategy.remote.control.topic=system-events
```

All strategies are registered by name, with fallback mechanisms to `BasicStrategy`.

---

## Testing & Validation

Each plugin includes:
- Unit tests using mock agent states
- Scenario simulations via test harness
- Optional integration tests if linked to external model inference

CI tools run plugin validation before deployment to avoid breaking downstream decisions.

---

## Example Strategies

| Strategy Name         | Description                                      |
|-----------------------|--------------------------------------------------|
| `BasicStrategy`       | Stateless fallback using hardcoded rules         |
| `TerrainAwareStrategy`| Weighs movement decisions using map elevation    |
| `RiskAdjustedStrategy`| Modulates behavior under volatile market inputs  |
| `CollaborativeStrategy`| Coordinates across agent clusters using shared goals |

---

## Future Improvements

- Graph-based strategies using decision trees
- Cloud-distributed strategies backed by external inference APIs
- Visual strategy builder for rapid prototyping

Plugins will remain language-agnostic as long as they conform to the interface and can be loaded dynamically.
