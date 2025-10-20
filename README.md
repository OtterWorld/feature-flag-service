# Feature Flag Service (FFS)

[![Kotlin](https://img.shields.io/badge/Kotlin-1.9.25-blue.svg)](https://kotlinlang.org)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5.6-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **A production-ready, reactive feature flag management service built with Spring Boot and Kotlin**

## 📋 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [Architecture](#-architecture)
- [Quick Start](#-quick-start)
- [API Documentation](#-api-documentation)
- [Development](#-development)
- [Docker Setup](#-docker-setup)
- [Learning Path](#-learning-path)
- [Testing](#-testing)
- [Monitoring & Observability](#-monitoring--observability)
- [Security](#-security)
- [Frontend](#-frontend-coming-soon)
- [Contributing](#-contributing)
- [License](#-license)
- [Resources](#-resources)
- [Support](#-support)

## 🎯 Overview

Feature Flag Service (FFS) is a modern, reactive microservice for managing feature flags across your applications. Built with Spring Boot WebFlux and Kotlin coroutines, it provides high-performance, non-blocking feature flag evaluation and management.

### What are Feature Flags?

Feature flags (also known as feature toggles) allow you to:
- **Enable/disable features** without deploying code
- **Progressive rollouts** - gradually release features to users
- **A/B testing** - test different features with different user segments
- **Kill switches** - quickly disable problematic features in production
- **Development in production** - hide incomplete features from users

## ✨ Key Features

### Core Functionality

- ✅ **Create, Read, Update, Delete** feature flags
- ✅ **Fast flag evaluation** - optimized for high-throughput read operations
- ✅ **Redis caching** - sub-millisecond flag evaluation
- ✅ **Reactive architecture** - non-blocking I/O for maximum concurrency
- ✅ **PostgreSQL persistence** - reliable, ACID-compliant storage
- ✅ **RESTful API** - easy integration with any client
- ✅ **Real-time updates** - instant flag changes across all services

### Technical Features

- 🚀 **Reactive Stack** - Spring WebFlux + R2DBC + Reactive Redis
- 🔒 **Security** - OAuth2 authentication and authorization
- 📊 **Observability** - Health checks, metrics, and monitoring
- 🧪 **Comprehensive Testing** - Unit, integration, and contract tests
- 🐳 **Docker Support** - Easy deployment with Docker and Docker Compose
- 📝 **Database Migrations** - Version-controlled schema with Flyway
- 🎨 **API Documentation** - Auto-generated with Spring REST Docs

## 🏗️ Architecture

### Technology Stack

| Layer | Technology |
|-------|------------|
| **Language** | Kotlin 1.9.25 |
| **Framework** | Spring Boot 3.5.6 (WebFlux) |
| **Database** | PostgreSQL 15+ (R2DBC) |
| **Cache** | Redis 7+ (Reactive) |
| **Security** | Spring Security + OAuth2 |
| **Build** | Gradle 8.5+ (Kotlin DSL) |
| **Runtime** | JDK 21 |
| **Testing** | JUnit 5, Testcontainers, MockK |

### Project Structure

```
feature-flag-service/
├── backend/                    # Spring Boot application
│   ├── src/main/kotlin/
│   │   └── com/feature_flag_service/
│   │       ├── controller/     # REST endpoints
│   │       ├── service/        # Business logic
│   │       ├── repository/     # Data access
│   │       ├── domain/         # Domain models
│   │       ├── dto/            # Data transfer objects
│   │       └── config/         # Configuration
│   └── src/main/resources/
│       ├── application.yml     # Application config
│       └── db/migration/       # Flyway migrations
├── domain/                     # Shared domain models
├── client-sdk/                 # Client library (future)
├── frontend/                   # React app (future)
├── docker/                     # Docker configurations
└── docs/                       # Documentation
```

## 🚀 Quick Start

### Prerequisites

- **Docker** and **Docker Compose** (recommended)
- **JDK 21** (if running without Docker)
- **Gradle 8.5+** (or use included wrapper)

### Option 1: Docker Compose (Recommended)

Start the entire stack with a single command:

```bash
# Clone the repository
git clone <repository-url>
cd feature-flag-service

# Start all services (PostgreSQL, Redis, Backend)
docker-compose up -d

# View logs
docker-compose logs -f backend

# Stop all services
docker-compose down
```

The API will be available at: **http://localhost:8080**

### Option 2: Local Development

```bash
# Start dependencies only (PostgreSQL + Redis)
docker-compose up -d postgres redis

# Run the application
./gradlew bootRun

# Or run with specific profile
./gradlew bootRun --args='--spring.profiles.active=dev'
```

### Verify Installation

```bash
# Check health
curl http://localhost:8080/actuator/health

# Expected response:
# {"status":"UP"}
```

## 📖 API Documentation

### Base URL

```
http://localhost:8080/api/v1
```

### Core Endpoints

#### 1. List All Feature Flags

```bash
GET /api/v1/flags

curl http://localhost:8080/api/v1/flags
```

**Response:**
```json
[
  {
    "id": 1,
    "key": "dark-mode",
    "name": "Dark Mode",
    "description": "Enable dark theme UI",
    "enabled": true,
    "createdAt": "2024-01-15T10:00:00",
    "updatedAt": "2024-01-15T10:00:00"
  }
]
```

#### 2. Get Feature Flag by ID

```bash
GET /api/v1/flags/{id}

curl http://localhost:8080/api/v1/flags/1
```

#### 3. Create Feature Flag

```bash
POST /api/v1/flags
Content-Type: application/json

curl -X POST http://localhost:8080/api/v1/flags \
  -H "Content-Type: application/json" \
  -d '{
    "key": "new-dashboard",
    "name": "New Dashboard",
    "description": "Redesigned dashboard UI",
    "enabled": false
  }'
```

**Response:** `201 Created`
```json
{
  "id": 2,
  "key": "new-dashboard",
  "name": "New Dashboard",
  "description": "Redesigned dashboard UI",
  "enabled": false,
  "createdAt": "2024-01-15T11:00:00",
  "updatedAt": "2024-01-15T11:00:00"
}
```

#### 4. Update Feature Flag

```bash
PUT /api/v1/flags/{id}
Content-Type: application/json

curl -X PUT http://localhost:8080/api/v1/flags/1 \
  -H "Content-Type: application/json" \
  -d '{
    "enabled": true
  }'
```

#### 5. Delete Feature Flag

```bash
DELETE /api/v1/flags/{id}

curl -X DELETE http://localhost:8080/api/v1/flags/1
```

**Response:** `204 No Content`

#### 6. Evaluate Feature Flag (Public Endpoint)

This is the **primary endpoint** for client applications to check if a feature is enabled.

```bash
POST /api/v1/flags/evaluate
Content-Type: application/json

curl -X POST http://localhost:8080/api/v1/flags/evaluate \
  -H "Content-Type: application/json" \
  -d '{"key": "dark-mode"}'
```

**Response:**
```json
{
  "key": "dark-mode",
  "enabled": true,
  "evaluatedAt": "2024-01-15T12:00:00"
}
```

### Actuator Endpoints

Monitor application health and metrics:

```bash
# Health check
curl http://localhost:8080/actuator/health

# Detailed health
curl http://localhost:8080/actuator/health | jq

# Metrics
curl http://localhost:8080/actuator/metrics

# Prometheus metrics (for monitoring)
curl http://localhost:8080/actuator/prometheus
```

## 💻 Development

### Build Commands

```bash
# Build the project
./gradlew build

# Build without tests
./gradlew build -x test

# Run tests
./gradlew test

# Run specific test
./gradlew test --tests "FeatureFlagServiceTest"

# Clean build
./gradlew clean build

# Check code quality
./gradlew check
```

### Run Application

```bash
# Default profile
./gradlew bootRun

# Development profile (verbose logging)
./gradlew bootRun --args='--spring.profiles.active=dev'

# Production profile
./gradlew bootRun --args='--spring.profiles.active=prod'
```

### Database Migrations

```bash
# Migrations run automatically on startup
# View migration status via Actuator
curl http://localhost:8080/actuator/flyway
```

**Create new migration:**

Create file: `backend/src/main/resources/db/migration/V{X}__{description}.sql`

Example: `V4__add_user_targeting.sql`

```sql
ALTER TABLE feature_flags
  ADD COLUMN target_users TEXT[];
```

## 🐳 Docker Setup

### Full Stack Deployment

The project includes a complete Docker Compose configuration for easy local development and deployment.

#### Architecture

```
┌─────────────────┐
│   Backend API   │ :8080
│  (Spring Boot)  │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼──┐  ┌──▼───┐
│ Postgres │  │ Redis  │
│  :5432  │  │ :6379 │
└─────────┘  └────────┘
```

#### Quick Start

```bash
# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f backend

# Check status
docker-compose ps

# Stop all services
docker-compose down

# Stop and remove volumes (clean slate)
docker-compose down -v
```

#### Service URLs

| Service | URL | Credentials |
|---------|-----|-------------|
| **Backend API** | http://localhost:8080 | N/A |
| **PostgreSQL** | localhost:5432 | postgres/postgres |
| **Redis** | localhost:6379 | No password |

#### Docker Compose Services

**PostgreSQL Database**
- Image: `postgres:15-alpine`
- Port: `5432`
- Database: `feature_flags`
- Persistent volume: `postgres-data`

**Redis Cache**
- Image: `redis:7-alpine`
- Port: `6379`
- Persistent volume: `redis-data`

**Backend Application**
- Built from: `./Dockerfile`
- Port: `8080`
- Depends on: PostgreSQL, Redis

### Build and Push Docker Image

```bash
# Build Docker image
docker build -t feature-flag-service:latest .

# Run container
docker run -d \
  -p 8080:8080 \
  -e SPRING_R2DBC_URL=r2dbc:postgresql://host.docker.internal:5432/feature_flags \
  -e SPRING_DATA_REDIS_HOST=host.docker.internal \
  --name ffs-backend \
  feature-flag-service:latest

# View logs
docker logs -f ffs-backend

# Stop container
docker stop ffs-backend
docker rm ffs-backend
```

### Environment Variables

Configure the application using environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `SPRING_R2DBC_URL` | PostgreSQL connection URL | `r2dbc:postgresql://localhost:5432/feature_flags` |
| `SPRING_R2DBC_USERNAME` | Database username | `postgres` |
| `SPRING_R2DBC_PASSWORD` | Database password | `postgres` |
| `SPRING_DATA_REDIS_HOST` | Redis host | `localhost` |
| `SPRING_DATA_REDIS_PORT` | Redis port | `6379` |
| `SERVER_PORT` | Application port | `8080` |
| `SPRING_PROFILES_ACTIVE` | Active profile | `default` |

**Example with custom configuration:**

```bash
docker-compose up -d postgres redis

docker run -d \
  -p 8080:8080 \
  -e SPRING_R2DBC_URL=r2dbc:postgresql://host.docker.internal:5432/feature_flags \
  -e SPRING_R2DBC_USERNAME=admin \
  -e SPRING_R2DBC_PASSWORD=secret \
  -e SPRING_PROFILES_ACTIVE=prod \
  feature-flag-service:latest
```

### Docker Development Workflow

```bash
# 1. Start dependencies
docker-compose up -d postgres redis

# 2. Run application locally (for development)
./gradlew bootRun

# 3. Make changes and rebuild
./gradlew build

# 4. Build new Docker image
docker build -t feature-flag-service:dev .

# 5. Update docker-compose to use new image
docker-compose up -d --build backend

# 6. Test
curl http://localhost:8080/actuator/health
```

### Accessing Services

**Connect to PostgreSQL:**
```bash
docker-compose exec postgres psql -U postgres -d feature_flags

# Run SQL queries
SELECT * FROM feature_flags;
SELECT * FROM flyway_schema_history;
```

**Connect to Redis:**
```bash
docker-compose exec redis redis-cli

# Check cached flags
KEYS flag:*
GET flag:dark-mode
```

**View Backend Logs:**
```bash
# Follow logs
docker-compose logs -f backend

# Last 100 lines
docker-compose logs --tail=100 backend
```

### Troubleshooting

**Backend won't start:**
```bash
# Check if PostgreSQL is ready
docker-compose logs postgres | grep "ready to accept connections"

# Check Redis
docker-compose exec redis redis-cli ping
# Should return: PONG

# Restart backend
docker-compose restart backend
```

**Database connection issues:**
```bash
# Verify network
docker network inspect feature-flag-service_default

# Check PostgreSQL logs
docker-compose logs postgres
```

**Reset everything:**
```bash
# Stop and remove all containers and volumes
docker-compose down -v

# Remove images
docker rmi feature-flag-service:latest

# Start fresh
docker-compose up -d --build
```

## 🎓 Learning Path

This project includes a comprehensive **step-by-step learning guide** for mastering Spring Boot and reactive programming.

### For Developers New to Spring Boot

Follow the detailed learning path in [`docs/learning-path/`](./docs/learning-path/):

- 📚 **[Complete Roadmap](./docs/learning-path/00-ROADMAP.md)** - Start here
- 🎯 **13 Progressive Steps** - From basics to advanced
- 💡 **Hands-on Exercises** - Learn by building
- 🔍 **Deep Dives** - Understand Spring Boot internals

**Topics Covered:**
- Spring Boot core concepts and auto-configuration
- Dependency injection and IoC container
- Reactive programming with WebFlux
- R2DBC for reactive database access
- Spring Security and OAuth2
- Testing strategies
- Gradle multi-module monorepo

## 🧪 Testing

```bash
# Run all tests
./gradlew test

# Run with coverage
./gradlew test jacocoTestReport

# Run integration tests only
./gradlew integrationTest

# Run unit tests only
./gradlew test --tests "*Test"
```

### Test Categories

- **Unit Tests** - Service layer business logic
- **Integration Tests** - Database and repository layer (with Testcontainers)
- **API Tests** - REST endpoint testing with WebTestClient
- **Contract Tests** - API documentation with Spring REST Docs

## 📊 Monitoring & Observability

### Health Checks

```bash
# Liveness probe (Kubernetes)
curl http://localhost:8080/actuator/health/liveness

# Readiness probe (Kubernetes)
curl http://localhost:8080/actuator/health/readiness

# Detailed health
curl http://localhost:8080/actuator/health
```

### Metrics

```bash
# All metrics
curl http://localhost:8080/actuator/metrics

# Specific metric
curl http://localhost:8080/actuator/metrics/jvm.memory.used

# Prometheus format (for Grafana)
curl http://localhost:8080/actuator/prometheus
```

## 🔒 Security

### Authentication (Future)

The service is prepared for OAuth2 authentication. To enable:

1. Configure OAuth2 provider in `application.yml`
2. Uncomment security configuration in `SecurityConfig.kt`
3. Restart the application

### Authorization

Feature flag management endpoints will require:
- **Read access**: `flags:read` scope
- **Write access**: `flags:write` scope
- **Admin access**: `flags:admin` scope

Evaluation endpoint (`/api/v1/flags/evaluate`) is public for client applications.

## 📦 Frontend (Coming Soon)

The monorepo is prepared for a React frontend application.

**Placeholder setup:**

```bash
# Navigate to frontend directory
cd frontend

# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build
```

The frontend will integrate with the backend API and provide:
- Dashboard for managing feature flags
- Real-time flag status monitoring
- User-friendly flag creation and editing
- Analytics and usage metrics

## 🤝 Contributing

This is a learning project. Contributions are welcome!

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Resources

- [CLAUDE.md](./CLAUDE.md) - AI assistant guidance
- [Learning Path](./docs/learning-path/00-ROADMAP.md) - Comprehensive tutorials
- [Spring Boot Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Kotlin + Spring](https://docs.spring.io/spring-framework/reference/languages/kotlin.html)
- [Project Reactor](https://projectreactor.io/docs/core/release/reference/)

## 💬 Support

- **Issues**: [GitHub Issues](https://github.com/your-org/feature-flag-service/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/feature-flag-service/discussions)
- **Email**: your-email@example.com

---

**Built with ❤️ using Kotlin and Spring Boot**
