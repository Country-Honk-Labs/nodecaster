# NodeCaster Architecture

## Overview

NodeCaster is a distributed decision orchestration system built on loosely coupled microservices. Each module operates independently and communicates through Kafka topics, allowing for full isolation, scalability, and flexible strategy experimentation.

This documentation outlines the structure and interaction patterns between core components.

---

## Core Components

### Agents

Agents are stateful services implemented in Java or Kotlin. Each agent:

- Operates on an internal lifecycle
- Emits status updates to Kafka
- Awaits decision responses to adapt behavior

Agents are extensible and autonomous, capable of functioning under different strategy modules.

### Decision Engine

Implemented in Kotlin with Spring Boot, the AI Engine is responsible for interpreting agent state and context data to issue decisions.

- Exposes a REST API (`POST /decide`)
- Contains rule-based heuristics or model invocation logic
- Returns decisions to be consumed by agents

This service is fully isolated and does not require state persistence across requests.

### Strategy Plugins

Plugins are modular strategy implementations written in Java. Each plugin implements the shared `DecisionStrategy` interface:

```java
interface DecisionStrategy {
    AgentAction decide(AgentState state, EnvironmentData env);
}
```

They are loaded on demand and can be switched dynamically via configuration or external triggers.

### Kafka Topics

Kafka acts as the central message broker. It allows asynchronous communication between services.

Recommended topics include:

- `agent-state` — published by agents to reflect their status
- `strategy-decisions` — outputs from plugins and AI Engine
- `system-events` — optional topic for global coordination

Services are designed to consume and publish events in a non-blocking manner.

---

## Event Flow

1. Agent emits current state to `agent-state` topic
2. Decision Engine consumes the event and processes it
3. Strategy Plugins may also consume the event (if activated)
4. Decision is returned to `strategy-decisions` topic
5. Agent consumes and acts upon the decision

This pipeline allows parallel processing, fallback mechanisms, and observability at each step.

---

## Deployment Strategy

All components are containerized via Docker and orchestrated using `docker-compose`. Each service has its own Dockerfile and runs in a separate container.

- Kafka broker and ZooKeeper are included by default
- Agents are distributed across separate containers
- Decision Engine is hosted independently
- Plugin modules may be embedded or launched dynamically

Environment variables and `.env` files control runtime configuration.

---

## Design Goals

- Full decoupling via event-driven architecture
- Simple onboarding using a single repository
- Extensibility for new strategies and agent types
- Interoperable AI layer with support for future model integration
- Clean separation of concerns across services

---

## Future Considerations

- Add frontend visualizer for agent interactions
- Dynamic strategy selection via web dashboard
- Distributed tracing with OpenTelemetry
- Multi-agent simulations for batch decision modeling
