# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Agent Coordination Strategy

### Main Agent Role
As the main agent, you coordinate with specialized sub-agents to handle complex tasks:
- **Direct user communication only** - Users interact exclusively with the main agent
- **Task delegation** - Assign appropriate sub-agents based on task type and complexity
- **Result aggregation** - Collect sub-agent outputs and synthesize comprehensive responses
- **Progress reporting** - Keep users informed of overall task progress and outcomes

### Sub-Agent Delegation Rules
1. **architect** → High-level system design, domain modeling, architectural decisions
2. **developer** → Code implementation, DDD patterns, business logic
3. **reviewer** → Code quality analysis, DDD compliance reviews, documentation
4. **tester** → Test strategy, test implementation, quality assurance

### Task Routing Guidelines
- **Architecture Tasks** → Use `architect` for bounded context design, aggregate modeling
- **Implementation Tasks** → Use `developer` for entities, repositories, services
- **Code Review Tasks** → Use `reviewer` for DDD compliance and quality analysis  
- **Testing Tasks** → Use `tester` for test strategies and test implementation
- **Complex Workflows** → Coordinate multiple agents sequentially (architect → developer → tester → reviewer)

## Common Development Commands

### Development and Build
- `npm run dev` - Start application in watch mode (development)
- `npm run build` - Build the application 
- `npm run start` - Start the application in production mode
- `npm run start:prod` - Start built application from dist/ directory

### Code Quality
- `npm run lint` - Run ESLint on TypeScript files
- `npm run format` - Format code using Prettier
- `npm test` - Run Jest unit tests
- `npm run test:watch` - Run tests in watch mode
- `npm run test:cov` - Run tests with coverage report
- `npm run test:e2e` - Run end-to-end tests

### Protocol Buffer Generation
- `make gen-all` - Generate all TypeScript files from proto definitions
- `make gen-auth` - Generate auth service TypeScript from proto
- `make gen-order` - Generate order service TypeScript from proto
- `make gen-portfolio` - Generate portfolio service TypeScript from proto
- `make gen-securities` - Generate securities service TypeScript from proto
- `make gen-credentials` - Generate credentials service TypeScript from proto
- `make gen-user` - Generate user service TypeScript from proto
- `make gen-order-history` - Generate order history service TypeScript from proto

### Database Operations
- `npm run migration:generate` - Generate a new database migration
- `npm run migration:run` - Run pending migrations
- `npm run migration:revert` - Revert the last migration
- `npm run schema:drop` - Drop database schema

## Project Architecture

### Core Framework
This is a **NestJS-based API Gateway** that acts as a microservices gateway using **gRPC** to communicate with backend services. The application follows a modular architecture with clear separation of concerns.

### Key Architectural Patterns

#### 1. gRPC Client Architecture
- **Generated TypeScript clients** from Protocol Buffer definitions in `src/protos/`
- **gRPC service injection** pattern using `@Inject(SERVICE_NAME)` with `ClientGrpc`
- **Metadata-based authentication** - JWT tokens passed via gRPC metadata headers
- **Error handling and retry logic** with configurable retry policies in `grpc.util.ts`
- **Request/Response transformation** between REST DTOs and gRPC proto messages

#### 2. Module Structure
Each business domain is organized into self-contained modules:
- `auth/` - Authentication and authorization (Cognito integration)
- `order/` - Order placement, cancellation, modification
- `order-history/` - Historical order data retrieval
- `portfolio/` - Portfolio and position management
- `securities/` - Market data and securities information
- `credentials/` - Broker account credentials management
- `user/` - User profile management

#### 3. Service Layer Pattern
Each module follows a consistent service pattern:
- **Controller** - REST API endpoints with Swagger documentation
- **Service** - Business logic and gRPC client orchestration
- **DTOs** - Request/response data transfer objects with validation
- **Domain** - Business domain models

#### 4. gRPC Communication Patterns
- **Authenticated calls** - Services requiring authentication pass JWT via gRPC metadata
- **Request ID generation** - Unique request IDs for tracing: `req_${timestamp}_${random}`
- **Timestamp handling** - ISO string timestamps for request/response correlation
- **Response mapping** - Transform gRPC responses to REST API format

#### 5. Configuration Management
- **Environment-based config** using `@nestjs/config` with validation
- **Type-safe configuration** with TypeScript interfaces
- **gRPC connection settings** in dedicated config modules
- **AWS Cognito integration** for authentication configuration

### Important Implementation Notes

#### Protocol Buffer Workflow
1. Proto files are stored in `src/protos/src/`
2. Generated TypeScript interfaces go to `src/protos/`  
3. Use Makefile commands to regenerate after proto changes
4. Generated files include NestJS-compatible service clients

#### gRPC Service Integration
- Services are registered in module providers using `ClientsModule.register()`
- gRPC clients are injected using service name constants (e.g., `AUTH_SERVICE_NAME`)
- All services implement `OnModuleInit` to initialize gRPC clients
- Use `firstValueFrom()` to convert Observable responses to Promises

#### Authentication Flow
- JWT tokens received from authentication service
- Tokens passed to downstream services via gRPC metadata
- Refresh token handling for token renewal
- Cognito integration for user management

#### Error Handling Strategy
- Global exception filter for gRPC errors (`GatewayExceptionFilter`)
- Retry logic for transient failures (network, timeout, etc.)
- HTTP status code mapping from gRPC status codes
- Structured error responses with request correlation IDs

### Environment Setup Requirements

#### AWS Authentication
```bash
aws codeartifact login --tool npm --domain stockgpt --domain-owner 851725573450 --repository stockgpt-artifactory-npm
```

#### Required Environment Variables
- `GRPC_URL_OMS` - gRPC service endpoint
- `COGNITO_USER_POOL_ID`, `COGNITO_CLIENT_ID`, `COGNITO_REGION` - AWS Cognito settings
- `APP_PORT` - Application port (default: 3000)
- `NODE_ENV` - Environment (development/production/test)

#### Dependencies
- Node.js >= 16.0.0
- Protocol Buffer compiler (`protoc`) >= 3.12
- Redis for caching (check with `lsof -i :6379`)

### Testing and Quality Assurance
- **Unit tests** using Jest with TypeScript support
- **E2E tests** with dedicated test configuration
- **Linting** with ESLint and TypeScript rules
- **Code formatting** with Prettier
- **Coverage reporting** available via Jest

### Deployment
- **Docker support** with multi-stage builds
- **Environment-specific configurations** via .env files
- **Health checks** provided by `@onst/common-lib`
- **Logging and monitoring** with structured logging interceptors