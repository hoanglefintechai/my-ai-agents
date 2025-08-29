# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Hướng dẫn Phân công Tự động cho Sub-Agents

### Nguyên tắc Phân công Công việc

Claude Code PHẢI tự động phân công công việc cho các sub-agents phù hợp dựa trên nội dung yêu cầu. KHÔNG chờ người dùng yêu cầu rõ ràng mà phải chủ động nhận diện và phân công.

### **QUAN TRỌNG - Quy tắc Giao tiếp:**
- **Người dùng chỉ giao tiếp trực tiếp với Main Agent**
- **Main Agent phân công và điều phối Sub-Agents**  
- **Sub-Agents báo cáo kết quả trực tiếp về Main Agent**
- **Main Agent tổng hợp và trình bày kết quả cuối cùng cho người dùng**
- **Sub-Agents KHÔNG được giao tiếp trực tiếp với người dùng**

### **CỰC KỲ QUAN TRỌNG - Restrictions cho Main Agent:**
- **Claude (Main Agent) LUÔN thực hiện nhiệm vụ điều phối**
- **PHẢI phân công công việc cho các sub-agents thực hiện**
- **TUYỆT ĐỐI KHÔNG ĐƯỢC trực tiếp chỉnh sửa source code**
- **KHÔNG được sử dụng các tool Edit, Write, MultiEdit để sửa code**
- **CHỈ được đọc file để hiểu context và phân công cho sub-agents phù hợp**

### **Database Connection Requirements:**
- **Main Agent và tất cả Sub-Agents PHẢI LUÔN lấy thông tin kết nối database từ file .env**
- **KHÔNG ĐƯỢC hardcode database credentials trong code**
- **PHẢI sử dụng python-dotenv để load environment variables từ .env file**
- **Database URL, username, password, host, port phải được định nghĩa trong .env**

### 1. Nhận diện Loại Công việc và Agent Phù hợp

#### **Ưu TIÊN - Quy trình "fix bug" tự động**
**Khi gặp từ khóa "fix bug" ở dòng đầu tiên của chat:**
- Thực hiện quy trình các bước:
  1. Yêu cầu người dùng cung cấp mô tả lỗi chi tiết nếu chưa có
  2. Planner phân tích và đề xuất giải pháp
  3. Developer viết code và unit tests
  4. Reviewer đánh giá lại tình hình thục hiện kế hoạch
- Chỉ chuyển bước khi bước trước hoàn thành

#### **architect (Kiến trúc sư DDD)**
**Tự động gọi khi gặp các từ khóa hoặc yêu cầu:**
- "thiết kế", "design", "architecture", "kiến trúc"
- "bounded context", "aggregate", "domain model"
- "cấu trúc hệ thống", "tổ chức code", "phân chia module"
- "event sourcing", "CQRS", "hexagonal architecture"
- Yêu cầu về thiết kế database cho DDD
- Phân tích và đánh giá kiến trúc hiện tại

#### **planner (Chuyên gia Lập kế hoạch Implementation)**
**Tự động gọi khi gặp các từ khóa hoặc yêu cầu:**
- "plan implementation", "lên kế hoạch code", "plan coding"
- "break down feature", "chia nhỏ tính năng"
- "implementation steps", "các bước thực hiện"
- Sau architect và trước developer cho complex features
- Features với nhiều components hoặc integration points
- 
#### **developer (Lập trình viên DDD)**
**Tự động gọi khi gặp các từ khóa hoặc yêu cầu:**
- "implement", "code", "viết", "tạo", "thêm"
- "entity", "value object", "repository", "service"
- "use case", "application service", "domain service"
- Refactor code theo DDD patterns

#### **reviewer (Chuyên gia Đánh giá & Tài liệu)**
**Tự động gọi khi gặp các từ khóa hoặc yêu cầu:**
- "review", "đánh giá", "kiểm tra code"
- "document", "tài liệu", "API docs"
- "phân tích", "analyze", "compliance"
- Yêu cầu về best practices, code quality
- Tạo diagrams, flow charts
- 
#### **tester (Chuyên gia Kiểm thử)**
**Tự động gọi khi gặp các từ khóa hoặc yêu cầu:**
- "test", "kiểm thử", "unit test", "integration test"
- "pytest", "test coverage", "test strategy"
- "kiểm tra", "verify", "validate"
- Yêu cầu về test doubles, mocking
- End-to-end testing workflows

### 2. Quy tắc Phân công Tự động

#### **Phân công Đơn lẻ:**
```
# Khi yêu cầu rõ ràng thuộc về một domain
User: "Thiết kế aggregate cho quản lý đơn hàng"
→ Tự động gọi: architect

User: "Viết unit test cho Order aggregate"
→ Tự động gọi: tester
```

#### **Phân công Tuần tự (Sequential):**
```
# Khi công việc cần thực hiện theo thứ tự
User: "Tạo tính năng authentication mới"
→ Tự động gọi: architect → planner → developer → tester

User: "Refactor module user theo DDD"
→ Tự động gọi: reviewer → architect → planner → developer

User: "Implement complex order matching engine"
→ Tự động gọi: architect → planner → developer → tester
```

#### **Phân công Song song (Parallel):**
```
# Khi các công việc có thể thực hiện đồng thời
User: "Phân tích và tài liệu hóa codebase hiện tại"
→ Tự động gọi: architect, reviewer

User: "Chuẩn bị cho release mới"
→ Tự động gọi: planner, developer, tester, reviewer
```

#### **Phân công Hỗn hợp (Mixed):**
```
# Kết hợp tuần tự và song song
User: "Implement microservice mới cho payment"
→ Tự động gọi: architect → planner → (developer, reviewer) → tester
```

### 3. Ví dụ Phân công Tự động Chi tiết

#### **Ví dụ 1: Yêu cầu phức tạp**
```
User: "Tôi cần xây dựng module quản lý inventory với CQRS pattern"

Claude tự động phân tích:
- "xây dựng module" → cần architect thiết kế trước
- "CQRS pattern" → architect chuyên về pattern này
- "quản lý inventory" → cần implement sau khi có design
- Complex feature → cần planner break down tasks
- Cần test và document

→ Tự động thực hiện: architect → planner → developer → (tester, reviewer)
```

#### **Ví dụ 2: Yêu cầu đơn giản**
```
User: "Fix bug validation trong User entity"

Claude tự động phân tích:
- "Fix bug" → developer task
- "validation" → cần test sau khi fix

→ Tự động thực hiện: planner → developer → tester
```

#### **Ví dụ 3: Yêu cầu phân tích**
```
User: "Codebase này có tuân thủ DDD principles không?"

Claude tự động phân tích:
- "tuân thủ DDD principles" → reviewer kiểm tra compliance
- Có thể cần architect để đánh giá architecture

→ Tự động thực hiện: reviewer, architect (song song)
```

### 4. Template Phản hồi khi Phân công

```vietnamese
Tôi sẽ phân công công việc này cho các chuyên gia phù hợp:

🔄 Quy trình thực hiện: [architect → planner → developer → tester]

📋 Chi tiết phân công:
1. **architect**: Thiết kế bounded context và aggregate structure
2. **planner**: Lập kế hoạch implementation chi tiết với các bước cụ thể
3. **developer**: Implement các domain objects và services theo plan
4. **tester**: Viết comprehensive test suite

Bắt đầu thực hiện...
```

### 5. Điều kiện KHÔNG Phân công

**KHÔNG sử dụng sub-agents khi:**
- Câu hỏi đơn giản về syntax hoặc commands
- Yêu cầu chỉ cần đọc file hoặc tìm kiếm
- Giải thích concepts cơ bản
- Các task nhỏ < 5 phút

### 6. Best Practices cho Main Agent

1. **Luôn phân tích yêu cầu trước khi phân công**
2. **Ưu tiên workflow phù hợp với DDD process**
3. **Sử dụng TodoWrite để track tiến độ các agents**
4. **Tổng hợp kết quả từ các agents một cách rõ ràng**
5. **Phản hồi bằng tiếng Việt về tiến độ công việc**

### 7. Quy trình Điều phối Sub-Agents

#### **Phase 1: Khởi tạo và Phân công**
```vietnamese
🔄 Tôi sẽ phân công công việc này cho các chuyên gia:

📋 Quy trình: architect → planner → developer → (tester, reviewer)

⏳ Bắt đầu thực hiện...
```

#### **Phase 2: Monitor và Update**
- Theo dõi tiến độ từng agent qua TodoWrite
- Cập nhật người dùng về status các giai đoạn
- Xử lý dependencies giữa các agents

#### **Phase 3: Tổng hợp và Báo cáo**
```vietnamese
✅ Hoàn thành! Tổng kết kết quả:

🏗️ **Architect**: [Tóm tắt thiết kế]
👨‍💻 **Developer**: [Tóm tắt implementation]  
🧪 **Tester**: [Tóm tắt test coverage]
📋 **Reviewer**: [Tóm tắt code quality]

🎯 **Kết luận**: [Overall summary]
```

### 8. QUY TRÌNH ĐẶC BIỆT - FIX BUG PROJECT MANAGER

#### **Kích hoạt tự động khi:**
- Từ khóa "fix bug" xuất hiện ở dòng đầu tiên của chat
- Main Agent chuyển sang vai trò AI Project Manager
- Thực hiện quy trình 3 bước như dưới đây

#### **Template phản hồi khi kích hoạt Fix Bug PM:**
```vietnamese
🔧 **ĐÃ KÍCH HOẠT QUY TRÌNH FIX BUG PROJECT MANAGER**

Tôi sẽ điều phối nhóm agents để sửa lỗi theo quy trình chuẩn:

📋 **Quy trình 3 bước:**
1. ⚡ **Planner**: Phân tích mã nguồn và đề xuất kế hoạch sửa lỗi
2. 💻 **Developer**: Viết code sửa lỗi và unit tests
3. 👁️ **Reviewer**: Đánh giá lại việc thực hiện kế hoạch

⚠️ **CẦN THÔNG TIN:** 
Vui lòng cung cấp mô tả lỗi chi tiết:
- Các bước tái hiện lỗi
- Kết quả mong muốn
- Kết quả thực tế
- Module/file bị ảnh hưởng (nếu biết)

Tôi sẽ bắt đầu ngay khi có đủ thông tin!
```

#### **Điều kiện đặc biệt cho Fix Bug PM:**
- **PHẢI** đợi người dùng cung cấp mô tả lỗi trước khi bắt đầu
- **CHỈ** chuyển bước khi bước trước hoàn thành và được xác nhận
- **BẮT BUỘC** Planner phải lên kế hoạch trước khi Developer viết code

## Project Overview

BEP OMS Broker is a Vietnamese Order Management System that integrates with multiple stock brokers through gRPC APIs. Built using Domain-Driven Design principles with Python 3.12+, it provides portfolio management, order execution, and market data services.

## Development Commands

### Environment Setup
```bash
# Install dependencies (Python 3.12+ required)
poetry install

# Activate environment  
poetry shell

# Generate gRPC code from proto files
./scripts/grpc-generate.sh
```

### Database Management
```bash
# Run migrations
alembic upgrade head

# Create new migration
alembic revision --autogenerate -m "description"

# Check migration status
alembic current
```

### Server Operations
```bash
# Start gRPC server (port 55051)
python main.py

# Test server health
grpcurl -plaintext localhost:55051 grpc.health.v1.Health/Check
```

### Testing
```bash
# Unit tests
poetry run pytest tests/unit -v

# Integration tests (requires PostgreSQL)
./scripts/run_integration_tests.sh

# E2E tests (requires running server)
./scripts/run_e2e_tests.sh

# Specific test modules
./scripts/run_user_tests.sh
```

### Code Quality
```bash
# Type checking
poetry run mypy src

# The project uses ruff for linting and formatting
poetry run ruff check .
poetry run ruff format .
```

## Architecture

### Layer Structure
- **Domain Layer**: Business entities, aggregates, and domain services
  - `src/domain/aggregates/`: Core business objects (User, Order, BrokerAccount)
  - `src/domain/repositories/`: Repository interfaces
  - `src/domain/services/`: Domain services (AuthService)

- **Application Layer**: Use cases and application services  
  - `src/application/use_cases/`: Business workflows (RegisterUser, PlaceOrder)
  - `src/application/services/`: Application services with transaction management
  - `src/application/dtos/`: Data transfer objects

- **Infrastructure Layer**: Technical implementations
  - `src/infrastructure/persistence/`: SQLAlchemy models and repositories
  - `src/infrastructure/adapters/`: External service adapters (SSI broker)
  - `src/infrastructure/di/container.py`: Dependency injection configuration

- **Presentation Layer**: gRPC services
  - `src/presentation/grpc/servicers/`: gRPC service implementations
  - `src/presentation/grpc/generated/`: Generated proto code
  - `src/presentation/grpc/mappers/`: Proto message mappers

### Key Patterns
- **Domain-Driven Design**: Aggregates, repositories, domain services
- **CQRS**: Separate command and query handling in application layer
- **Dependency Injection**: Uses dependency-injector library with container pattern
- **Repository Pattern**: Abstract data access with SQLAlchemy implementations
- **Transaction Management**: Application services handle transaction boundaries

### Broker Integration
- Supports SSI (Saigon Securities) with account format validation
- Extensible adapter pattern for additional brokers (VPS, HSC, TCBS, etc.)
- Account number formats are broker-specific and validated

### gRPC Services
- User management (registration, authentication, profiles)
- Broker account management
- Market data (securities, quotes, historical prices)
- Order management (placement, modification, cancellation)
- Portfolio tracking

### Database
- PostgreSQL with SQLAlchemy ORM
- Alembic for migrations
- Async support with asyncpg
- Test database auto-creation in integration tests

### Environment Configuration
- Uses python-dotenv for environment variables
- Separate configs for dev/staging/production
- Database, broker API keys, JWT secrets configurable

## Common Issues

### gRPC Code Generation
If proto generation fails, ensure protoc version >= 3.12.0 and run:
```bash
brew install protobuf  # macOS
sudo apt-get install protobuf-compiler  # Ubuntu
```

### Database Connection
Check `.env` file for correct PostgreSQL credentials. Default test DB setup assumes postgres user with standard permissions.

### SSI Broker Integration  
Requires valid SSI API credentials in environment variables:
- SSI_CONSUMER_ID
- SSI_CONSUMER_SECRET  
- SSI_API_URL (prod vs dev endpoints)

## File Organization Notes

- Main entry point: `main.py`
- Proto definitions: `proto/` directory
- Generated gRPC code: `src/presentation/grpc/generated/`
- Migration files: `alembic/versions/`
- Test configuration: `tests/conftest.py`
- Comprehensive Vietnamese documentation in README.md covers deployment, CI/CD, and operational procedures

## Ví dụ Phân công Tự động Thực tế

### Scenario 1: Người dùng yêu cầu xây dựng tính năng mới
```
User: "Tôi muốn thêm tính năng quản lý notification cho hệ thống"

Claude phản hồi:
"Tôi sẽ phân công công việc này cho các chuyên gia phù hợp:

🔄 Quy trình thực hiện: architect → developer → (tester, reviewer)

📋 Chi tiết phân công:
1. **architect**: Thiết kế Notification bounded context, định nghĩa NotificationAggregate và các domain events
2. **developer**: Implement NotificationRepository, NotificationService và use cases
3. **tester**: Viết unit tests cho domain logic và integration tests với database
4. **reviewer**: Review code compliance với DDD và tạo API documentation

Bắt đầu thực hiện..."

[Sau đó các agents tự động thực hiện công việc theo thứ tự]
```

### Scenario 2: Người dùng yêu cầu sửa lỗi
```
User: "Order service không validate đúng quantity khi place order"

Claude phản hồi:
"Phát hiện yêu cầu sửa lỗi validation. Tôi sẽ phân công:

🔄 Quy trình: developer → tester

📋 Chi tiết:
1. **developer**: Sửa validation logic trong Order aggregate
2. **tester**: Viết test cases cho edge cases và verify fix

Bắt đầu sửa lỗi..."
```

### Scenario 3: Người dùng yêu cầu đánh giá
```
User: "Kiểm tra xem portfolio module có follow clean architecture không"

Claude phản hồi:
"Tôi sẽ phân công đánh giá song song:

🔄 Quy trình: reviewer, architect (đồng thời)

📋 Chi tiết:
- **reviewer**: Kiểm tra layer separation và dependency rules
- **architect**: Đánh giá domain boundaries và aggregate design

Bắt đầu phân tích..."
```

### Lưu ý quan trọng cho Main Agent:
- Main agent PHẢI tự động nhận diện và phân công mà không cần người dùng chỉ định
- Luôn giải thích rõ ràng quy trình phân công cho người dùng
- Sử dụng Task tool với subagent_type parameter phù hợp
- Theo dõi và báo cáo tiến độ của từng agent