# Environment Module

## Overview

The environment layer in NodeCaster represents external context data that agents use to make decisions. This module serves as the input signal space for strategy plugins, the decision engine, and agents themselves.

Environment data is ingested, processed, and broadcasted via Kafka, allowing all services to react to consistent world-state representations.

---

## Core Concepts

- **Contextual Signals**: Variables such as terrain, temperature, market data, crowd metrics, or system pressure.
- **Shared Visibility**: Environment data is available to all agents and decision services.
- **Temporal Validity**: Each data point carries a timestamp and may expire or be overridden.

---

## Data Sources

Environment data may come from:

- Sensor simulations or feed emulators
- Manual overrides via configuration
- External APIs (e.g., weather, finance, telemetry)
- Agent observations (collaborative reporting)

Each source is normalized and published to Kafka under the `environment-data` topic.

---

## Example Payload

```json
{
  "source": "weather-sim",
  "timestamp": "2025-07-25T12:20:00Z",
  "location": "Brisbane",
  "signals": {
    "temperature": 22.5,
    "humidity": 60,
    "windDirection": "NE",
    "stormLevel": "low"
  }
}
```

The structure may vary based on domain.

---

## Consumption Model

- **Agents**: Query or subscribe to signals relevant to their task domain.
- **Decision Engine**: Incorporates environment data into decision scoring.
- **Strategy Plugins**: May filter, transform, or threshold data before applying logic.

Environment updates are either pushed (Kafka events) or pulled (internal queries) depending on configuration.

---

## Integration

Environment modules may be:

- Stateless listeners that emit events into Kafka
- Periodic tasks that refresh signals at intervals
- Reactive to agent feedback (creating feedback loops)

Docker containers may include mounting of config files, external API keys, or event injectors.

---

## Best Practices

- Maintain domain-specific schemas per environment source
- Annotate signals with source and confidence level
- Use time-based windowing for data aggregation when needed

---

## Future Extensions

- Geo-fenced signal routing for agent localization
- Agent-generated environment traces (collaborative mapping)
- Anomaly detection services for signal validation
