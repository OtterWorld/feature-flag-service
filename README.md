# Feature Flag Service (FFS) - Monorepo

[![Kotlin](https://img.shields.io/badge/Kotlin-1.9.25-blue.svg)](https://kotlinlang.org)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.5.6-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-18.3-blue.svg)](https://reactjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.5-blue.svg)](https://www.typescriptlang.org)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

> **A production-ready, full-stack feature flag management system with reactive backend and modern React frontend**

## 📋 Table of Contents

- [Overview](#-overview)
- [Monorepo Structure](#-monorepo-structure)
- [Key Features](#-key-features)
- [Architecture](#-architecture)
- [Quick Start](#-quick-start)
- [API Documentation](#-api-documentation)
- [Development](#-development)
- [Docker Setup](#-docker-setup)
- [Frontend Application](#-frontend-application)
- [Learning Path](#-learning-path)
- [Testing](#-testing)
- [Monitoring & Observability](#-monitoring--observability)
- [Security](#-security)
- [Deployment](#-deployment)
- [Contributing](#-contributing)
- [License](#-license)
- [Resources](#-resources)
- [Support](#-support)

## 🎯 Overview

Feature Flag Service (FFS) is a modern, full-stack application for managing feature flags across your applications. The system consists of:

- **Backend**: Reactive Spring Boot microservice built with Kotlin and WebFlux
- **Frontend**: Modern React application with TypeScript and Vite

### What are Feature Flags?

Feature flags (also known as feature toggles) allow you to:
- **Enable/disable features** without deploying code
- **Progressive rollouts** - gradually release features to users
- **A/B testing** - test different features with different user segments
- **Kill switches** - quickly disable problematic features in production
- **Development in production** - hide incomplete features from users

## 📁 Monorepo Structure

```
feature-flag-service/
├── backend/                    # Spring Boot Kotlin backend
│   ├── src/
│   │   ├── main/kotlin/       # Application source
│   │   └── test/kotlin/       # Tests
│   ├── build.gradle.kts       # Gradle build configuration
│   ├── Dockerfile             # Backend Docker image
│   ├── docs/                  # Backend documentation
│   └── README.md              # Backend-specific docs
│
├── frontend/                   # React TypeScript frontend
│   ├── src/
│   │   ├── components/        # Reusable React components
│   │   ├── pages/             # Page components
│   │   ├── services/          # API integration services
│   │   ├── hooks/             # Custom React hooks
│   │   ├── types/             # TypeScript type definitions
│   │   ├── contexts/          # React contexts
│   │   ├── utils/             # Utility functions
│   │   └── assets/            # Static assets
│   ├── public/                # Public static files
│   ├── package.json           # Node dependencies
│   ├── vite.config.ts         # Vite configuration
│   ├── Dockerfile             # Multi-stage Docker image
│   ├── nginx.conf             # Production nginx config
│   └── README.md              # Frontend-specific docs
│
├── docker-compose.yml         # Local development (both services)
├── docker-compose.prod.yml    # Production reference
├── .env.example               # Environment variables template
├── .gitignore                 # Monorepo gitignore
├── CLAUDE.md                  # AI assistant instructions
└── README.md                  # This file
```

### Service Deployment

Each service can be **deployed independently** for CI/CD:
- **Backend**: `backend/Dockerfile` - Can be built and deployed separately
- **Frontend**: `frontend/Dockerfile` - Multi-stage build (dev/prod targets)
- **Local Dev**: `docker-compose.yml` - Runs both services together

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

### Backend Project Structure

```
backend/src/main/kotlin/com/feature_flag_service/
├── controller/                 # REST endpoints
├── service/                    # Business logic
├── repository/                 # Data access layer
├── domain/                     # Domain models
├── dto/                        # Data transfer objects
└── config/                     # Spring configuration
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

# Start all services (PostgreSQL, Redis, Backend, Frontend)
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down
```

**Services will be available at:**
- Frontend: **http://localhost:3000**
- Backend API: **http://localhost:8080**

### Option 2: Local Development

```bash
# Start dependencies only (PostgreSQL + Redis)
docker-compose up -d postgres redis

# Run backend (from backend directory)
cd backend
./gradlew bootRun --args='--spring.profiles.active=test'

# Run frontend (in another terminal, from frontend directory)
cd frontend
npm install
npm run dev
```

### Verify Installation

```bash
# Check backend health
curl http://localhost:8080/actuator/health
# Expected response: {"status":"UP"}

# Check frontend (open in browser)
open http://localhost:3000
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

### Backend Development

All backend commands should be run from the `backend/` directory:

```bash
cd backend

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

**Run Backend Application:**

```bash
cd backend

# Default profile
./gradlew bootRun

# Development profile (verbose logging)
./gradlew bootRun --args='--spring.profiles.active=dev'

# Test profile (with Testcontainers)
./gradlew bootRun --args='--spring.profiles.active=test'

# Production profile
./gradlew bootRun --args='--spring.profiles.active=prod'
```

### Frontend Development

All frontend commands should be run from the `frontend/` directory:

```bash
cd frontend

# Install dependencies
npm install

# Start development server (http://localhost:3000)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Type checking
npm run type-check

# Lint code
npm run lint
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

The monorepo includes Docker Compose configurations for both local development and production reference.

#### Architecture

```
┌─────────────────┐
│   Frontend UI   │ :3000
│  (React/Vite)   │
└────────┬────────┘
         │
         │ API calls
         ▼
┌─────────────────┐
│   Backend API   │ :8080
│  (Spring Boot)  │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
┌───▼──┐  ┌──▼───┐
│Postgres│  │ Redis │
│ :5432  │  │:6379 │
└────────┘  └───────┘
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
| **Frontend UI** | http://localhost:3000 | N/A |
| **Backend API** | http://localhost:8080 | N/A |
| **PostgreSQL** | localhost:5432 | postgres/postgres |
| **Redis** | localhost:6379 | No password |

#### Docker Compose Services

**Frontend Application (React)**
- Built from: `./frontend/Dockerfile`
- Port: `3000`
- Target: `development` (with hot reload)
- Depends on: Backend

**Backend Application (Spring Boot)**
- Built from: `./backend/Dockerfile`
- Port: `8080`
- Depends on: PostgreSQL, Redis

**PostgreSQL Database**
- Image: `postgres:15-alpine`
- Port: `5432`
- Database: `feature_flags`
- Persistent volume: `postgres-data`

**Redis Cache**
- Image: `redis:7-alpine`
- Port: `6379`
- Persistent volume: `redis-data`

### Build and Push Docker Images

**Backend:**
```bash
cd backend

# Build backend Docker image
docker build -t feature-flag-backend:latest .

# Run backend container
docker run -d \
  -p 8080:8080 \
  -e SPRING_R2DBC_URL=r2dbc:postgresql://host.docker.internal:5432/feature_flags \
  -e SPRING_DATA_REDIS_HOST=host.docker.internal \
  --name ffs-backend \
  feature-flag-backend:latest
```

**Frontend:**
```bash
cd frontend

# Build frontend for development
docker build --target development -t feature-flag-frontend:dev .

# Build frontend for production
docker build --target production -t feature-flag-frontend:prod .

# Run frontend container (development)
docker run -d -p 3000:3000 --name ffs-frontend feature-flag-frontend:dev

# Run frontend container (production)
docker run -d -p 80:80 --name ffs-frontend-prod feature-flag-frontend:prod
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

# 2. Run services locally (for development with hot reload)
# Terminal 1 - Backend
cd backend
./gradlew bootRun

# Terminal 2 - Frontend
cd frontend
npm run dev

# 3. Or rebuild and restart services with Docker
docker-compose up -d --build

# 4. Test
curl http://localhost:8080/actuator/health
open http://localhost:3000
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

## 🎨 Frontend Application

The React frontend provides a modern UI for managing feature flags.

### Technology Stack
- **React 18.3** - UI library
- **TypeScript 5.5** - Type safety
- **Vite 5.x** - Fast build tool and dev server
- **React Router 6** - Client-side routing
- **Axios** - HTTP client for API calls

### Directory Structure
```
frontend/src/
├── components/     # Reusable UI components
├── pages/          # Page-level components
├── services/       # API integration layer
├── hooks/          # Custom React hooks
├── types/          # TypeScript type definitions
├── contexts/       # React context providers
├── utils/          # Helper functions
└── assets/         # Static assets (images, icons)
```

### API Integration
The frontend communicates with the backend via:
- **Development**: Vite proxy to `http://localhost:8080`
- **Production**: Nginx proxy to backend service

### Building for Production
```bash
cd frontend
npm run build  # Creates optimized build in dist/
```

For more details, see [frontend/README.md](frontend/README.md).

## 🎓 Learning Path

This project includes a comprehensive **step-by-step learning guide** for mastering Spring Boot and reactive programming.

### For Developers New to Spring Boot

Follow the detailed learning path in [`backend/docs/learning-path/`](./backend/docs/learning-path/):

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

### Backend Tests

```bash
cd backend

# Run all tests
./gradlew test

# Run with coverage
./gradlew test jacocoTestReport

# Run integration tests only
./gradlew integrationTest

# Run unit tests only
./gradlew test --tests "*Test"
```

**Test Categories:**
- **Unit Tests** - Service layer business logic
- **Integration Tests** - Database and repository layer (with Testcontainers)
- **API Tests** - REST endpoint testing with WebTestClient
- **Contract Tests** - API documentation with Spring REST Docs

### Frontend Tests

```bash
cd frontend

# Run tests (once implemented)
npm test

# Run tests in watch mode
npm test -- --watch

# Run tests with coverage
npm test -- --coverage
```

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

## 🚢 Deployment

### Separate Service Deployment (CI/CD Ready)

The monorepo is designed for independent deployment of frontend and backend:

**Backend Deployment:**
```bash
cd backend
docker build -t your-registry/feature-flag-backend:1.0.0 .
docker push your-registry/feature-flag-backend:1.0.0
```

**Frontend Deployment:**
```bash
cd frontend
docker build --target production -t your-registry/feature-flag-frontend:1.0.0 .
docker push your-registry/feature-flag-frontend:1.0.0
```

### Production Docker Compose

For reference, use `docker-compose.prod.yml`:

```bash
# Copy and configure environment variables
cp .env.example .env
# Edit .env with production values

# Start production stack
docker-compose -f docker-compose.prod.yml up -d
```

### CI/CD Strategies

The monorepo supports multiple CI/CD approaches:

1. **Path-based Triggers** - Build only changed services
   ```yaml
   # Example GitHub Actions
   on:
     push:
       paths:
         - 'backend/**'  # Only build backend if changed
         - 'frontend/**' # Only build frontend if changed
   ```

2. **Separate Pipelines** - Independent workflows for each service
3. **Monorepo Pipeline** - Single pipeline with conditional builds

### Environment Variables

See `.env.example` for all configurable options.

**Key variables:**
- `POSTGRES_PASSWORD` - Database password
- `SPRING_PROFILES_ACTIVE` - Backend profile (dev/prod)
- `VITE_API_URL` - Frontend API endpoint
- `CORS_ALLOWED_ORIGINS` - Allowed CORS origins

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

### Documentation
- [Backend Documentation](./backend/README.md) - Backend-specific details
- [Frontend Documentation](./frontend/README.md) - Frontend-specific details
- [CLAUDE.md](./CLAUDE.md) - AI assistant guidance
- [Learning Path](./backend/docs/learning-path/00-ROADMAP.md) - Comprehensive tutorials

### External Resources
- [Spring Boot Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/)
- [Kotlin + Spring](https://docs.spring.io/spring-framework/reference/languages/kotlin.html)
- [Project Reactor](https://projectreactor.io/docs/core/release/reference/)
- [React Documentation](https://react.dev/)
- [Vite Documentation](https://vitejs.dev/)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

## 💬 Support

- **Issues**: [GitHub Issues](https://github.com/your-org/feature-flag-service/issues)
- **Discussions**: [GitHub Discussions](https://github.com/your-org/feature-flag-service/discussions)
- **Email**: your-email@example.com

---

**Built with ❤️ using Kotlin, Spring Boot, React, and TypeScript**
