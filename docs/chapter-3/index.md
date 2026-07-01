# ⚡ Chapter 3: FastAPI

*Mastering FastAPI.*
Build lightning-fast, async REST APIs with auto-generated Swagger documentation.
**Estimated Reading Time:** 30 min

---

!!! info "Java Analogy"
    FastAPI is basically what Spring Boot would look like if it went on a diet, learned async, and returned with auto-generated Swagger docs built in. The moment you run your server and open `/docs` — seeing your entire API documented and testable without a single Swagger config line — is genuinely exciting.

---

## 1. Why FastAPI?

FastAPI is built on Starlette (for the HTTP layer) and Pydantic (for data validation). 

**FastAPI vs Spring Boot**
- **Speed:** Startup time is in milliseconds, compared to seconds for Spring Boot.
- **Auto-generated Swagger:** It generates OpenAPI (Swagger) docs automatically from your Pydantic models and function signatures. No `@Operation` or `@Parameter` annotations required!
- **Async First:** Built from the ground up for high-concurrency non-blocking I/O.

---

## 2. Project Structure

Unlike Spring Boot, FastAPI does not force a specific folder structure. However, to keep Spring developers comfortable, we highly recommend replicating a standard 3-tier architecture.

**Recommended Folder Structure**

```text
app/
│
├── main.py          # Application entry point (like @SpringBootApplication)
├── routes/          # Handles HTTP requests (like @RestController)
├── services/        # Contains business logic (like @Service)
├── repositories/    # Database operations (like @Repository)
├── schemas/         # Pydantic models (like DTOs)
├── models/          # SQLAlchemy ORM models (like @Entity)
├── database.py      # DB connection setup
├── dependencies.py  # Shared dependencies (Auth, DB session)
└── config.py        # Application configuration
```

!!! tip "Key Takeaway"
    Organize your Python project exactly like your Spring Boot project. It works perfectly.

---

## 3. Creating Your First API

In Spring, you use `@RestController` and `@RequestMapping`. In FastAPI, you simply define an `app` object and attach decorators directly to your functions.

```python
from fastapi import FastAPI

app = FastAPI()

# Equivalent to @GetMapping("/")
@app.get("/")
async def hello():
    return {"message": "Hello"}
```

---

## 4. Path, Query & Request Parameters

FastAPI automatically parses and validates parameters based on where they appear in the function signature.

| Spring Boot | FastAPI |
|---|---|
| `@PathVariable Long id` | `user_id: int` (in path) |
| `@RequestParam String q` | `q: str` (in signature, not in path) |
| `@RequestBody UserDTO dto` | `user: UserDTO` (inherits from BaseModel) |

**Example:**
```python
@app.get("/users/{user_id}")
async def get_user(
    user_id: int,           # Path parameter
    active: bool = True     # Query parameter (default True)
):
    return {"id": user_id, "active": active}
```

---

## 5. Request & Response Models

In Spring Boot, you use DTOs and Jackson. In FastAPI, you use Pydantic `BaseModel`.

To control what data is returned to the client (hiding passwords or internal IDs), use the `response_model` argument in the route decorator.

```python
from pydantic import BaseModel

class UserCreate(BaseModel):
    username: str
    password: str

class UserResponse(BaseModel):
    id: int
    username: str

@app.post("/users", response_model=UserResponse)
async def create_user(user: UserCreate):
    # Returns only what is defined in UserResponse!
    return {"id": 1, "username": user.username, "password": user.password}
```

---

## 6. CRUD Operations

FastAPI supports all standard HTTP methods. Here is how a full CRUD flow looks for a Student resource.

??? example "Complete CRUD Routes"
    ```python
    from fastapi import FastAPI, HTTPException
    from pydantic import BaseModel
    from typing import List

    app = FastAPI()

    class Student(BaseModel):
        id: int
        name: str

    db: dict[int, Student] = {}

    @app.post("/students", response_model=Student, status_code=201)
    async def create(student: Student):
        db[student.id] = student
        return student

    @app.get("/students", response_model=List[Student])
    async def read_all():
        return list(db.values())

    @app.get("/students/{id}", response_model=Student)
    async def read_one(id: int):
        if id not in db:
            raise HTTPException(status_code=404, detail="Not found")
        return db[id]
        
    @app.put("/students/{id}", response_model=Student)
    async def update(id: int, student: Student):
        db[id] = student
        return student

    @app.delete("/students/{id}", status_code=204)
    async def delete(id: int):
        del db[id]
    ```

---

## 7. Dependency Injection

Spring Boot uses `@Autowired` and constructor injection. FastAPI uses `Depends()` — a function whose return value is injected into the route handler.

```python
from fastapi import Depends

def get_db_session():
    # Setup connection
    yield "db_session"
    # Teardown connection

@app.get("/users")
async def list_users(db = Depends(get_db_session)):
    return {"db": db}
```

!!! warning "⚠️ The Dependency Scope Trap (Spring Boot vs FastAPI)"
    In Spring Boot, a `@Service` bean is typically a **singleton** created once and reused throughout the application's lifetime. 
    
    In FastAPI, a dependency created with `Depends()` is **resolved for each request by default**. 
    
    Keep dependencies lightweight! **Do not** initialize expensive resources (like loading a 500MB ML model or establishing a database connection pool) inside a dependency function. Instead, create those resources once during application startup, and use `Depends()` only to access the already-initialized instance.

---

## 8. Services & Repository Pattern

To make Spring developers comfortable, separate your logic. Routes should handle HTTP, Services handle business logic, and Repositories handle the database.

**The Flow:**
`Routes (HTTP)` → `Services (Business Logic)` → `Repositories (SQL)` → `Database`

```python
from fastapi import APIRouter, Depends

router = APIRouter()

# Service injected via Depends
@router.post("/students")
async def enroll(data: StudentCreate, svc: StudentService = Depends(get_student_service)):
    return svc.enroll_student(data)
```

---

## 9. Error Handling

Instead of Spring's `@ControllerAdvice` and throwing custom exceptions everywhere, FastAPI provides two clean mechanisms:

1. **Inline HTTPExceptions:** Throw an error directly in a route.
2. **Global Exception Handlers:** Catch custom business exceptions globally.

```python
from fastapi import HTTPException, Request
from fastapi.responses import JSONResponse

# 1. Inline
@app.get("/items/{id}")
async def get_item(id: int):
    raise HTTPException(status_code=404, detail="Item not found")

# 2. Global (Like @ControllerAdvice)
class BusinessException(Exception):
    pass

@app.exception_handler(BusinessException)
async def handle_business_error(request: Request, exc: BusinessException):
    return JSONResponse(status_code=400, content={"error": "Business rule violated"})
```

---

## 10. Middleware

FastAPI allows you to inject custom logic before and after every request, just like a Spring `Filter` or `Interceptor`.

```python
import time
from fastapi import Request

@app.middleware("http")
async def add_process_time_header(request: Request, call_next):
    start_time = time.time()
    response = await call_next(request)  # Pass request down the chain
    process_time = time.time() - start_time
    response.headers["X-Process-Time"] = str(process_time)
    return response
```

---

## 11. Authentication & Security

FastAPI has built-in utilities for OAuth2. The standard way to secure endpoints is using `OAuth2PasswordBearer` as a dependency. This replaces the heavy machinery of Spring Security.

```python
from fastapi.security import OAuth2PasswordBearer
from fastapi import Depends

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="login")

# Require a valid token to access this route
@app.get("/secure-data")
async def secure_endpoint(token: str = Depends(oauth2_scheme)):
    return {"message": "You are authenticated!"}
```

*(Note: In a real app, you would decode the JWT token inside the dependency rather than just returning the raw string).*

---

## 12. Background Tasks

If you need to fire-and-forget a task (like sending a welcome email) without blocking the HTTP response, FastAPI has built-in support mapping directly to Spring's `@Async`.

```python
from fastapi import BackgroundTasks

def send_email(email: str):
    print(f"Sending email to {email}")

@app.post("/register")
async def register(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(send_email, email)
    return {"message": "Email queued in background"}
```

---

## 13. Lifespan Events

For operations that should happen exactly once when the application starts (like `@PostConstruct`) and once when it stops (like `@PreDestroy`), use a Lifespan context manager.

```python
from fastapi import FastAPI
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    print("Startup: Init DB pools (@PostConstruct)")
    yield
    print("Shutdown: Close connections (@PreDestroy)")

app = FastAPI(lifespan=lifespan)
```

---

## 14. Testing

Testing is a first-class citizen in FastAPI. You use `pytest` (JUnit equivalent) and `TestClient` (MockMvc equivalent). 

A massive advantage over Spring Boot is `app.dependency_overrides`, which lets you mock databases in a single line.

```python
from fastapi.testclient import TestClient

client = TestClient(app)

def test_hello():
    response = client.get("/")
    assert response.status_code == 200
```

---

## 15. Best Practices

1. **Keep routes thin:** Controllers should only parse HTTP and return JSON.
2. **Business logic in services:** Pure Python classes make logic easy to test.
3. **Separate schemas from ORM models:** Don't return SQLAlchemy objects directly to the client; always pass them through a Pydantic `response_model`.
4. **Use dependency injection:** It makes your app infinitely easier to test.
5. **Don't put SQL in routes:** Keep all database code in Repositories.

---

## 16. Spring Boot Comparison Summary

Here is your quick-reference cheat sheet for mapping Spring concepts to FastAPI.

| Spring Boot | FastAPI |
|---|---|
| `@RestController` | `APIRouter()` / `FastAPI()` |
| `@RequestMapping` | `@app.get()`, `@app.post()` |
| `@Autowired` | `Depends()` |
| `@Service` | Standard Python class |
| `@Repository` | Standard Python class |
| `@ControllerAdvice` | `@app.exception_handler` |
| Spring Security | `OAuth2PasswordBearer` + JWT |
| Filters / Interceptors | Middleware |
| `@Async` | `BackgroundTasks` |
| `@PostConstruct` | `lifespan` context manager |
