# AI Engine Documentation

## Overview

The AI Engine is a stateless microservice within the NodeCaster system responsible for making context-aware decisions based on input provided by other services. It exposes a REST endpoint that receives agent state and environmental context and returns a structured decision response. The engine logic may include rule-based heuristics, scoring systems, or external model integrations.

This service is implemented in Kotlin using the Spring Boot framework.

---

## Responsibilities

- Accept agent state and context via HTTP `POST /decide`
- Process the request using configured decision logic
- Return a structured decision in JSON format
- Optionally log or persist decision traces for debugging

---

## Directory Structure

```
ai-engine/
├── src/
│   └── main/
│       ├── kotlin/
│       │   └── com/nodecaster/aiengine/
│       │       ├── controller/
│       │       ├── service/
│       │       ├── model/
│       │       └── DecisionEngineApplication.kt
├── build.gradle.kts
└── Dockerfile
```

---

## API Specification

### `POST /decide`

**Description**: Accepts agent state and context, returns a decision object.

#### Request (JSON)

```json
{
  "agentId": "agent-001",
  "state": {
    "intent": "broadcast_message",
    "previousDecisions": ["wait", "observe"]
  },
  "context": {
    "location": "Brisbane",
    "audience": {
      "size": 120,
      "engagementLevel": "high"
    },
    "externalFactors": {
      "timeOfDay": "evening"
    }
  }
}
```

#### Response (JSON)

```json
{
  "decision": "initiate_broadcast",
  "confidence": 0.92,
  "metadata": {
    "strategy": "heuristic:rule-07",
    "timestamp": "2025-07-25T10:45:12Z"
  }
}
```

---

## Deployment

The microservice is containerized using Docker and integrated into the system via `docker-compose`.

### Sample Dockerfile (Kotlin)

```Dockerfile
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY build/libs/ai-engine.jar .
CMD ["java", "-jar", "ai-engine.jar"]
```

### `docker-compose.yaml` Fragment

```yaml
ai-engine:
  build:
    context: ./ai-engine
  ports:
    - "8081:8081"
  environment:
    - SPRING_PROFILES_ACTIVE=prod
```

---

## Logging and Observability

Structured logging is recommended for tracing decisions. OpenTelemetry-compatible metrics may be added for performance monitoring.

---

## Future Enhancements

- Model-based scoring integration via external ML service
- Decision trace storage in PostgreSQL
- GraphQL endpoint for richer agent queries
