# Python DDD Agents Collection

Specialized AI agents for Python Domain-Driven Design development with PostgreSQL, designed to enhance DDD workflows with domain-specific expertise.

## Overview

This collection contains 4 specialized agents focused exclusively on Python Domain-Driven Design (DDD) patterns and PostgreSQL integration. Each agent is an expert in specific aspects of DDD development, automatically invoked based on context or explicitly called when needed.

## Available Agents

### **[architect](architect.md) - Python DDD System Architect**
Specialized in Domain-Driven Design architecture for Python projects. Provides system overview, designs bounded contexts, aggregates, entities, value objects, and domain services. Expert in DDD patterns, hexagonal architecture, and microservice boundaries using Python and PostgreSQL.

**Use when:**
- Designing bounded contexts and ubiquitous language
- Creating aggregate boundaries and consistency models
- Implementing hexagonal/clean architecture patterns
- Database design with PostgreSQL for DDD
- Event sourcing and CQRS patterns
- System architecture and integration patterns

### **[developer](developer.md) - Python DDD Developer**
Expert Python developer specialized in Domain-Driven Design implementation. Masters Python DDD patterns, clean code, SOLID principles, and DDD-specific libraries. Implements entities, aggregates, repositories, domain services, and application services using idiomatic Python with proper typing and error handling.

**Use when:**
- Implementing DDD building blocks (entities, value objects, aggregates)
- Repository pattern implementation with SQLAlchemy
- Domain events and event handlers
- Application services and use cases
- Dependency injection and IoC containers
- Type hints and mypy for domain modeling

### **[reviewer](reviewer.md) - Python DDD Code Reviewer & Documenter**
Code review specialist for Python DDD projects. Reviews code for DDD principles compliance, clean architecture, Python best practices, and PostgreSQL integration. Creates comprehensive documentation including API docs, domain model diagrams, and process flow documentation.

**Use when:**
- Reviewing code for DDD pattern compliance
- Clean architecture layer separation validation
- Creating domain model documentation
- API documentation with examples
- Database schema documentation
- Process flow diagrams and integration patterns

### **[tester](tester.md) - Python DDD Testing Specialist**
Testing expert for Python DDD applications. Designs comprehensive test strategies including unit tests for domain logic, integration tests for repositories and database operations, end-to-end tests for complete workflows. Expert in pytest, test doubles, and testing DDD patterns with PostgreSQL.

**Use when:**
- Unit testing for domain entities and aggregates
- Repository pattern testing with test doubles
- Integration testing with PostgreSQL
- Testing aggregate invariants and business rules
- Event handler and domain event testing
- End-to-end testing for complete workflows

## Technology Stack

### **Core Technologies**
- **Python 3.11+** - Primary development language
- **PostgreSQL** - Primary database for persistence and event sourcing
- **SQLAlchemy** - ORM and database abstraction layer
- **Pydantic** - Data validation and serialization
- **FastAPI** - HTTP API framework

### **DDD Libraries & Tools**
- **dataclasses** - Value objects and entity implementation
- **dependency-injector** - Dependency injection container
- **pytest** - Testing framework
- **Factory Boy** - Test data generation
- **testcontainers-python** - Integration testing with PostgreSQL
- **mypy** - Static type checking

### **Architecture Patterns**
- **Domain-Driven Design (DDD)** - Core design philosophy
- **Hexagonal Architecture** - Dependency inversion and clean architecture
- **CQRS** - Command Query Responsibility Segregation
- **Event Sourcing** - Event-driven state management
- **Repository Pattern** - Data access abstraction
- **Unit of Work** - Transaction management

## Usage Examples

### Automatic Invocation
These agents will be automatically selected based on task context:

```bash
# Architecture and design tasks → architect (Python DDD System Architect)
"Design a user management bounded context for an e-commerce system"
"Create aggregate boundaries for order processing"

# Implementation tasks → developer (Python DDD Developer)  
"Implement a User aggregate with email validation"
"Create a repository for Order entities using SQLAlchemy"

# Code review tasks → reviewer (Python DDD Code Reviewer & Documenter)
"Review this aggregate implementation for DDD compliance"
"Create API documentation for the user management service"

# Testing tasks → tester (Python DDD Testing Specialist)
"Write unit tests for the Order aggregate business rules"
"Create integration tests for the user repository"
```

### Explicit Invocation
Call specific agents by name:

```bash
# Architecture consultation
"Use architect to design a CQRS architecture for inventory management"

# Implementation guidance
"Have developer implement event sourcing for the audit trail"

# Code review and documentation
"Get reviewer to review the domain model and create documentation"

# Testing strategy
"Use tester to design a comprehensive testing strategy for the payment domain"
```

## Multi-Agent Workflows

### Execution Flow Rules

**Sequential Execution (với mũi tên →):**
- Khi sử dụng mũi tên `agent1 → agent2`, các tác vụ sẽ được thực hiện tuần tự
- Agent1 phải hoàn thành công việc trước khi agent2 bắt đầu
- Ví dụ: `architect → developer → tester` có nghĩa là architect làm xong → developer làm → tester làm cuối

**Parallel Execution (không có mũi tên):**
- Khi không có mũi tên chỉ dẫn, các agents sẽ thực hiện tác vụ song song
- Tất cả agents được gọi cùng lúc và làm việc độc lập
- Ví dụ: `architect, developer, tester` có nghĩa là cả 3 agents cùng làm việc song song

### Practical Examples

**Sequential Flow:**
```bash
# This will execute in order: architect → developer → tester
"architect → developer → tester: Implement user authentication"
# 1. architect creates design first
# 2. developer implements based on design  
# 3. tester creates tests based on implementation
```

**Parallel Flow:**
```bash
# This will execute simultaneously
"architect, reviewer: Analyze the current codebase"
# Both agents work on analysis at the same time
```

**Mixed Flow:**
```bash
# Sequential then parallel
"architect → (developer, tester): Design and implement user module"
# 1. architect creates design first
# 2. Then developer and tester work in parallel
```

These agents work together seamlessly for complex DDD implementations:

### Feature Development Workflow
```
User Story → architect (architecture) → developer (implementation) → tester (testing) → reviewer (review & docs)

Example: "Implement user registration with email verification"
1. architect: Design User aggregate and authentication bounded context
2. developer: Implement entities, repositories, and application services  
3. tester: Create unit and integration tests
4. reviewer: Review code and generate documentation
```

### Domain Modeling Workflow
```
Business Requirements → architect (domain design) → developer (implementation) → reviewer (documentation)

Example: "Model the inventory management domain"
1. architect: Define bounded contexts, aggregates, and domain events
2. developer: Implement domain objects with business rules
3. reviewer: Document domain model and create API specs
```

### Refactoring Workflow
```
Legacy Code → reviewer (analysis) → architect (redesign) → developer (implementation) → tester (testing)

Example: "Refactor user management to follow DDD principles"
1. reviewer: Analyze current code for DDD compliance issues
2. architect: Design proper domain boundaries and architecture
3. developer: Implement refactored domain objects
4. tester: Create tests for new implementation
```

## DDD Principles & Patterns

### Core DDD Concepts
- **Ubiquitous Language** - Shared vocabulary between domain experts and developers
- **Bounded Context** - Explicit boundaries for domain models
- **Aggregate** - Consistency boundaries and transactional units
- **Entity** - Objects with identity and lifecycle
- **Value Object** - Immutable objects without identity
- **Domain Service** - Stateless domain operations
- **Repository** - Collections of domain objects
- **Domain Event** - Something that happened in the domain

### Architecture Patterns
- **Layered Architecture** - Presentation, Application, Domain, Infrastructure
- **Hexagonal Architecture** - Ports and adapters pattern
- **Clean Architecture** - Dependency inversion and testability
- **CQRS** - Separate read and write models
- **Event Sourcing** - Store events instead of current state

### Python-Specific Patterns
- **Dataclasses** for value objects and entities
- **Protocol** interfaces for dependency inversion  
- **Type hints** for domain modeling clarity
- **Context managers** for unit of work pattern
- **Decorators** for cross-cutting concerns

## Communication and Language Requirements

### Vietnamese Language Usage
All agents in this collection MUST use Vietnamese language in ALL processes:

**Internal Processes:**
- **Thinking and reasoning** - Tất cả suy nghĩ phải bằng tiếng Việt
- **Search queries and analysis** - Tìm kiếm và phân tích bằng tiếng Việt  
- **Problem decomposition** - Phân tích vấn đề bằng tiếng Việt
- **Planning and strategy** - Lập kế hoạch bằng tiếng Việt

**External Communication:**
- **All responses to users** - Tất cả phản hồi bằng tiếng Việt
- **Documentation and comments** - Tài liệu và comment bằng tiếng Việt
- **Error messages and logs** - Thông báo lỗi bằng tiếng Việt
- **Status updates and progress reports** - Báo cáo tiến độ bằng tiếng Việt

**Tool Usage:**
- **Search descriptions** - Mô tả tìm kiếm bằng tiếng Việt
- **File operation descriptions** - Mô tả thao tác file bằng tiếng Việt  
- **Command explanations** - Giải thích lệnh bằng tiếng Việt

This ensures consistent Vietnamese communication throughout all agent workflows and makes the system more accessible to Vietnamese-speaking teams.

## Best Practices

### Domain Modeling
1. **Start with the domain** - Understand business rules before coding
2. **Use ubiquitous language** - Match code to business terminology
3. **Define clear boundaries** - Separate contexts explicitly
4. **Keep aggregates small** - Focus on consistency requirements
5. **Make implicit concepts explicit** - Turn business rules into code

### Python Implementation
6. **Use type hints extensively** - Improve code clarity and catch errors
7. **Favor immutability** - Especially for value objects
8. **Encapsulate business logic** - Keep domain rules in domain objects
9. **Separate concerns** - Clear layer boundaries and responsibilities
10. **Test business behavior** - Focus on domain logic, not implementation

### Database Integration
11. **Use repository pattern** - Abstract database access
12. **Design for consistency** - Respect aggregate boundaries
13. **Handle transactions properly** - Use unit of work pattern
14. **Optimize for queries** - Design read models for CQRS
15. **Plan for evolution** - Database migration strategies

## Installation

Place these agents in your Claude Code agents directory:

```bash
# Copy agents to Claude Code directory
cp -r agents/ ~/.claude/agents/
```

## File Structure

```
agents/
├── README.md     # This documentation
├── LICENSE       # MIT License
├── architect.md  # Python DDD System Architect
├── developer.md  # Python DDD Developer  
├── reviewer.md   # Python DDD Code Reviewer & Documenter
└── tester.md     # Python DDD Testing Specialist
```

## Contributing

To enhance these DDD agents:

1. **Follow DDD principles** - Ensure additions align with domain-driven design
2. **Focus on Python** - Maintain Python-specific expertise
3. **Include PostgreSQL** - Consider database patterns and optimization
4. **Provide examples** - Include concrete code examples
5. **Test thoroughly** - Validate recommendations with real scenarios

## Learn More

### DDD Resources
- [Domain-Driven Design by Eric Evans](https://www.domainlanguage.com/ddd/)
- [Implementing Domain-Driven Design by Vaughn Vernon](https://vaughnvernon.co/?page_id=168)
- [Architecture Patterns with Python](https://www.cosmicpython.com/)

### Python DDD Resources
- [Python DDD Implementation Guide](https://github.com/cosmicpython/code)
- [SQLAlchemy DDD Patterns](https://docs.sqlalchemy.org/en/14/orm/session_transaction.html)
- [FastAPI DDD Architecture](https://fastapi.tiangolo.com/advanced/async-sql-databases/)

### Tools & Libraries
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [SQLAlchemy Documentation](https://docs.sqlalchemy.org/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [pytest Documentation](https://docs.pytest.org/)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.