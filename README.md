# 🎧 BetterMusic — Event-Driven Microservices

*Software Architecture course (Architettura dei Sistemi Software) · Università degli Studi Roma Tre (A.Y. 2025/26)*
*Team: Francesco Giovanardi, Alessandro Cavina*

BetterMusic is a small social network for music album reviews. Users publish reviews and follow reviewers, artists or genres; their feed ("followed reviews") collects the reviews that match what they follow.

The course provides a working microservice system in which services call each other synchronously over REST. Our task was to **evolve it into an event-driven architecture**, so that services keep local copies of the data they need and no longer depend on each other being available.

## Architecture

| Service | Responsibility | Database |
|---|---|---|
| `album` | music albums (title, artist, genres) | PostgreSQL |
| `recensioni` | reviews (reviewer, album, text, summary) | PostgreSQL |
| `connessioni` | who follows which reviewer / artist / genre | PostgreSQL |
| `recensioni-seguite` | each user's feed of followed reviews | PostgreSQL |
| `api-gateway` | single entry point on port 8080 (Spring Cloud Gateway) | — |

Service discovery and load balancing use **Consul** + Spring Cloud LoadBalancer, with Resilience4j circuit breakers. Each service follows a **hexagonal (ports & adapters)** structure: domain, REST adapters, event publishers and consumers.

## What we built
- **Database per service**: migrated every service from in-memory HSQLDB to its own **PostgreSQL** instance (Docker Compose).
- **Kafka integration** (Spring Kafka, JSON-serialised events):
  - `album` publishes `AlbumCreatedEvent`;
  - `recensioni` consumes album events into a local album replica, so creating a review no longer calls the `album` service, and publishes `RecensioniCreatedEvent`;
  - `connessioni` publishes `ConnessioniCreatedEvent` / `ConnessioniDeletedEvent`.
- **Event contracts** in the shared `*-api` modules (one channel per service).
- **Infrastructure as code**: `docker-compose.yml` with Consul, Kafka and the four databases; Helm chart and scripts to run Kafka on Kubernetes.
- **End-to-end test** through the API gateway (`test_kafka_album_recensioni.sh`).

Detailed change log (Italian): [`log_modifiche.md`](log_modifiche.md) · Original assignment and service API (Italian): [`ASSIGNMENT.md`](ASSIGNMENT.md)

## Running it
Requirements: Java 21, Gradle, Docker.

```bash
docker compose up -d            # Consul, Kafka, PostgreSQL databases
gradle build                    # build all services
./start-bettermusic.sh          # start the services
./do-init-bettermusic-db.sh     # load sample data
./run-sample-queries.sh         # query the system through the gateway (localhost:8080)
./stop-bettermusic.sh
```

## Tech stack
Java · Spring Boot · Spring Cloud (Gateway, Consul Discovery, LoadBalancer, Resilience4j) · Spring Data JPA · Spring Kafka · Apache Kafka · PostgreSQL · Docker Compose · Kubernetes / Helm · Gradle
