# NodeCaster 🎛️

NodeCaster is a distributed decision-making engine built on a microservice architecture. It leverages Kafka for event-driven communication between agents, strategy plugins, and an AI decision backend.

This project serves as a showcase for scalable agent orchestration, plugin experimentation, and AI integration, all within a unified repository.

---

## Architecture Overview

NodeCaster is composed of loosely coupled microservices:

- **Java/Kotlin Agents**: Lifecycle-driven actors reacting to events and performing actions.
- **AI Engine (Spring Boot + Kotlin)**: Receives agent states and environment data; returns decisions using ML or heuristic logic.
- **Plugins (Java)**: Modular decision strategies (e.g., round-robin, priority-based, AI-driven).
- **Kafka Topics**: Central communication bus for distributing events and decisions across services.

Each component is containerized and orchestrated via Docker Compose for easy bootstrapping and isolation.

---

## Decision Flow

1. Agents publish their current state to a Kafka topic.
2. Plugin or AI service consumes the state and returns a decision.
3. Decision is published back to Kafka for agent consumption.
4. Agents update behavior accordingly and loop back.

This allows hot-swapping strategies, parallel experimentation, and total decoupling of business logic.

---

## Plugin System

Decision strategies are written as Java classes implementing the `DecisionStrategy` interface:

```java
public interface DecisionStrategy {
    AgentAction decide(AgentState state, EnvironmentData env);
}
```

Available plugins:

- RoundRobinStrategy
- PriorityBasedStrategy
- AiDrivenStrategy

You can add your own strategies to /plugins and register them via config or .env.

## Getting Started
```bash
git clone https://github.com/youruser/NodeCaster.git
cd NodeCaster
docker-compose up --build
```

This will launch:

- Kafka broker and ZooKeeper
- All agents (Java/Kotlin)
- AI engine (Spring Boot app)
- Plugin registry

### Project Structure
```
/docs              → Documentation and diagrams
/docker            → Dockerfiles and compose setup
/plugins           → Java strategy implementations
/agents/java       → Java agent implementations
/agents/kotlin     → Kotlin agent modules
/ai                → Python-based decision backend
/config            → Centralized config and .env files
README.md
```

🧪 Future Extensions

- Live plugin injection via REST
- Graphical agent visualizer (frontend)
- Web dashboard for switching strategies on the fly

## Author
Crafted by Diego, a creative engineer passionate about efficient systems, collaborative environments, and a touch of musical harmony.
