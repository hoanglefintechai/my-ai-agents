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

#### **Ưu TIÊN - Quy trình ưu tiên**
**Khi gặp từ khóa "do plan" ở dòng đầu tiên của chat:**
- Thực hiện lập kế hoạch chi tiết:
  1. Planner phân tích yêu cầu và context
  2. Tạo kế hoạch step-by-step 
  3. Ghi kế hoạch vào file docs/plan/{week-of-year}-{increase-number}-{task-name}-plan.md
- Chỉ sử dụng Planner agent

**Khi gặp từ khóa "do rev" ở dòng đầu tiên của chat:**
- Thực hiện review chi tiết:
  1. Reviewer phân tích code và thực hiện đánh giá
  2. Tạo báo cáo review toàn diện
  3. Ghi kết quả vào file docs/review/{week-of-year}-{increase-number}-{task-name}-review.md
- Chỉ sử dụng Reviewer agent

**Khi gặp từ khóa "do exe" ở dòng đầu tiên của chat:**
- Thực hiện implement theo kế hoạch:
  1. Developer đọc kế hoạch từ file plan hoặc yêu cầu
  2. Thực hiện implement code theo từng bước
  3. Báo cáo tiến độ và kết quả hoàn thành
- Chỉ sử dụng Developer agent

**Khi gặp từ khóa "fast fix" ở dòng đầu tiên của chat:**
- Thực hiện quy trình 3 bước nhanh:
  1. **Tìm nguyên nhân (Root Cause)**: Developer phân tích và tìm nguyên nhân lỗi
  2. **Lên kế hoạch (Planning)**: Planner phân tích vấn đề và đề xuất giải pháp cụ thể
  3. **Chỉnh sửa (Editing)**: Developer thực hiện chỉnh sửa code theo kế hoạch và báo cáo hoàn thành
- Không cần yêu cầu thông tin thêm, làm việc với thông tin hiện có
- Chỉ chuyển bước khi bước trước hoàn thành

**Khi gặp từ khóa "fix bug" ở dòng đầu tiên của chat:**
- Thực hiện quy trình 4 bước:
  1. **Root Cause**: Developer tìm nguyên nhân lỗi chi tiết
  2. **Plan**: Planner phân tích và đề xuất giải pháp chi tiết
  3. **Dev**: Developer viết code sửa lỗi và unit tests
  4. **Review**: Reviewer đánh giá việc thực hiện kế hoạch
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
- "Fix bug" → cần tìm root cause, plan, dev, review
- "validation" → cần test sau khi fix

→ Tự động thực hiện: developer → planner → developer → reviewer
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
- Thực hiện quy trình 4 bước như dưới đây

#### **Template phản hồi khi kích hoạt Fix Bug PM:**
```vietnamese
🔧 **ĐÃ KÍCH HOẠT QUY TRÌNH FIX BUG PROJECT MANAGER**

Tôi sẽ điều phối nhóm agents để sửa lỗi theo quy trình chuẩn:

📋 **Quy trình 4 bước:**
1. 🔍 **Root Cause**: Developer tìm nguyên nhân lỗi chi tiết
2. 📝 **Plan**: Planner phân tích và đề xuất kế hoạch sửa lỗi
3. 💻 **Dev**: Developer viết code sửa lỗi và unit tests
4. 👁️ **Review**: Reviewer đánh giá việc thực hiện kế hoạch

⏳ Bắt đầu thực hiện...
```

#### **Điều kiện đặc biệt cho Fix Bug PM:**
- **CHỈ** chuyển bước khi bước trước hoàn thành và được xác nhận
- **BẮT BUỘC** tuân thủ thứ tự: Root Cause → Plan → Dev → Review
- **PHẢI** có unit tests trong bước Dev

### 9. QUY TRÌNH ĐẶC BIỆT - FAST FIX

#### **Kích hoạt tự động khi:**
- Từ khóa "fast fix" xuất hiện ở dòng đầu tiên của chat
- Main Agent chuyển sang chế độ sửa nhanh 3 bước

#### **Template phản hồi khi kích hoạt Fast Fix:**
```vietnamese
⚡ **ĐÃ KÍCH HOẠT QUY TRÌNH FAST FIX**

Tôi sẽ thực hiện sửa nhanh theo quy trình 3 bước:

📋 **Quy trình Fast Fix:**
1. 🔍 **Tìm nguyên nhân (Root Cause)**: Developer phân tích và tìm nguyên nhân lỗi
2. 📝 **Lên kế hoạch (Planning)**: Planner phân tích và đề xuất giải pháp
3. ✏️ **Chỉnh sửa (Editing)**: Developer thực hiện sửa code

⏱️ Bắt đầu thực hiện ngay...
```

#### **Đặc điểm của Fast Fix:**
- **NHANH**: Chỉ 3 bước - tìm nguyên nhân, lên kế hoạch và chỉnh sửa
- **ĐƠN GIẢN**: Không yêu cầu thông tin thêm từ người dùng
- **TẬP TRUNG**: Chỉ sử dụng Developer và Planner
- **HIỆU QUẢ**: Phù hợp cho các sửa đổi nhỏ và rõ ràng
- **PHÂN TÍCH TRƯỚC**: Developer tìm root cause trước khi plan

#### **So sánh Fast Fix vs Fix Bug:**
| Tiêu chí | Fast Fix | Fix Bug |
|----------|----------|---------|
| Số bước | 3 bước | 4 bước |
| Bước đầu tiên | Developer tìm root cause | Developer tìm root cause |
| Agents sử dụng | Developer, Planner | Developer, Planner, Reviewer |
| Unit tests | Không bắt buộc | Bắt buộc |
| Review | Không | Có |
| Thời gian | Nhanh | Trung bình |

### 10. QUY TRÌNH ĐẶC BIỆT - DO PLAN

#### **Kích hoạt tự động khi:**
- Từ khóa "do plan" xuất hiện ở dòng đầu tiên của chat
- Cần lên kế hoạch chi tiết cho một task phức tạp
- Main Agent chuyển sang chế độ lập kế hoạch

#### **Template phản hồi khi kích hoạt Do Plan:**
```vietnamese
📋 **ĐÃ KÍCH HOẠT QUY TRÌNH DO PLAN**

Tôi sẽ điều phối Planner để lên kế hoạch chi tiết:

🎯 **Mục tiêu**: Tạo kế hoạch thực hiện chi tiết và lưu vào file

📝 **Planner sẽ**:
- Phân tích yêu cầu và context hiện tại
- Lập kế hoạch từng bước cụ thể
- Ghi kế hoạch vào file docs/plan/{week-of-year}-{increase-number}-{task-name}-plan.md

⏳ Bắt đầu lập kế hoạch...
```

#### **Đặc điểm của Do Plan:**
- **CHI TIẾT**: Planner tạo kế hoạch step-by-step
- **LƯU TRỮ**: Kế hoạch được ghi vào file docs/plan/{week-of-year}-{increase-number}-{task-name}-plan.md
- **TÁI SỬ DỤNG**: Có thể dùng lại kế hoạch cho các task tương tự
- **THEO DÕI**: Dễ dàng track progress theo kế hoạch đã lập

#### **Output của Do Plan:**
Planner sẽ tạo file docs/plan/{week-of-year}-{increase-number}-{task-name}-plan.md với cấu trúc:
```markdown
# Kế hoạch thực hiện: [Tên task]

## Tổng quan
[Mô tả ngắn gọn về task]

## Các bước thực hiện
1. **Bước 1**: [Mô tả chi tiết]
   - Sub-task 1.1
   - Sub-task 1.2
   
2. **Bước 2**: [Mô tả chi tiết]
   - Sub-task 2.1
   - Sub-task 2.2

## Dependencies
- [Liệt kê các phụ thuộc]

## Ước lượng thời gian
- Tổng thời gian: [X hours]
- Chi tiết từng bước...
```

### 11. QUY TRÌNH ĐẶC BIỆT - DO REV

#### **Kích hoạt tự động khi:**
- Từ khóa "do rev" xuất hiện ở dòng đầu tiên của chat
- Cần review code hoặc đánh giá chất lượng
- Main Agent chuyển sang chế độ review

#### **Template phản hồi khi kích hoạt Do Rev:**
```vietnamese
👁️ **ĐÃ KÍCH HOẠT QUY TRÌNH DO REV**

Tôi sẽ điều phối Reviewer để thực hiện đánh giá chi tiết:

🎯 **Mục tiêu**: Thực hiện review toàn diện và lưu báo cáo vào file

📋 **Reviewer sẽ**:
- Phân tích code quality và architecture
- Kiểm tra tuân thủ best practices và DDD principles
- Đánh giá security và performance
- Ghi báo cáo review vào file docs/review/{week-of-year}-{increase-number}-{task-name}-review.md

⏳ Bắt đầu review...
```

#### **Đặc điểm của Do Rev:**
- **TOÀN DIỆN**: Reviewer đánh giá nhiều khía cạnh (quality, security, performance)
- **LƯU TRỮ**: Kết quả review được ghi vào file docs/review/
- **TÁI SỬ DỤNG**: Có thể tham khảo cho các review tương tự
- **THEO DÕI**: Dễ dàng track các vấn đề và cải thiện

#### **Output của Do Rev:**
Reviewer sẽ tạo file docs/review/{week-of-year}-{increase-number}-{task-name}-review.md với cấu trúc:
```markdown
# Review Report: [Tên task]

## Tổng quan
[Mô tả ngắn gọn về nội dung được review]

## Code Quality Assessment
- **Điểm số**: [X/10]
- **Ưu điểm**: [Liệt kê các điểm tốt]
- **Vấn đề**: [Liệt kê các vấn đề cần sửa]

## Architecture & Design
- **DDD Compliance**: [Đánh giá tuân thủ DDD]
- **Layer Separation**: [Đánh giá phân tách layer]
- **Dependencies**: [Đánh giá dependency management]

## Security & Performance
- **Security Issues**: [Các vấn đề bảo mật]
- **Performance Concerns**: [Các vấn đề hiệu năng]

## Recommendations
1. [Khuyến nghị 1]
2. [Khuyến nghị 2]
3. [Khuyến nghị 3]

## Action Items
- [ ] [Todo item 1]
- [ ] [Todo item 2]
```

### 12. QUY TRÌNH ĐẶC BIỆT - DO EXE

#### **Kích hoạt tự động khi:**
- Từ khóa "do exe" xuất hiện ở dòng đầu tiên của chat
- Cần implement code theo kế hoạch có sẵn
- Main Agent chuyển sang chế độ execution

#### **Template phản hồi khi kích hoạt Do Exe:**
```vietnamese
💻 **ĐÃ KÍCH HOẠT QUY TRÌNH DO EXE**

Tôi sẽ điều phối Developer để thực hiện implement:

🎯 **Mục tiêu**: Implement code theo kế hoạch đã được định nghĩa

⚙️ **Developer sẽ**:
- Đọc và phân tích kế hoạch implementation
- Thực hiện code theo từng bước chi tiết
- Tuân thủ DDD patterns và best practices
- Báo cáo tiến độ và kết quả hoàn thành

⏳ Bắt đầu implement...
```

#### **Đặc điểm của Do Exe:**
- **TẬP TRUNG**: Chỉ sử dụng Developer agent
- **TUÂN THỦ KẾ HOẠCH**: Follow theo plan đã được lập trước
- **DDD COMPLIANCE**: Implement theo Domain-Driven Design principles
- **BÁO CÁO TIẾN ĐỘ**: Developer báo cáo từng bước thực hiện
- **KHÔNG REVIEW**: Chỉ implement, không có review step

#### **Quy trình Do Exe:**
1. **Đọc kế hoạch**: Developer phân tích plan từ file hoặc yêu cầu
2. **Implement từng bước**: 
   - Tạo domain objects (entities, value objects, aggregates)
   - Implement repositories và services
   - Tạo use cases và application services
   - Implement presentation layer (gRPC services)
3. **Tuân thủ standards**:
   - Follow DDD patterns
   - Use dependency injection
   - Handle transactions properly
   - Follow naming conventions
4. **Báo cáo kết quả**: Summary về những gì đã implement

#### **Khi nào sử dụng Do Exe:**
- Đã có kế hoạch chi tiết từ "do plan"
- Cần implement một feature hoàn chỉnh
- Muốn tập trung vào coding mà không cần planning/review
- Có yêu cầu rõ ràng về implementation

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