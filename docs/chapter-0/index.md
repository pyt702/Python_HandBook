# 🐍 Chapter 0: Python Basics

*Python Basics for Spring Boot Developers.*
Learn Python syntax, OOP, async programming, and the language features you'll use when building FastAPI and AI applications.
**Estimated Reading Time:** 25 min

---

!!! note
    If you've been writing Java for years, Python is going to feel weirdly short. No semicolons, no closing braces — but it's very real code. Every concept below maps directly to something you already know from Java and Spring Boot.


## 1. Setup & Virtual Environments

Java uses Maven/Gradle to manage dependencies per project. Python uses virtual environments (venv) to isolate dependencies. Think of it as your personal project sandbox — like creating a new Maven project folder, but at the runtime level.

| Java / Spring Boot | Python Equivalent |
|---|---|
| `mvn install` / `gradle build` | `pip install -r requirements.txt` |
| `pom.xml` / `build.gradle` | `requirements.txt` / `pyproject.toml` |
| `mvn spring-boot:run` | `uvicorn main:app --reload` |
| New Maven project folder | `python -m venv myenv` |

**Example — Creating and activating a virtual environment**

```
# Create a virtual environment
python -m venv myenv

# Activate (Linux/Mac)
source myenv/bin/activate

# Activate (Windows)
myenv\Scripts\activate

# Install packages (like Maven dependencies)
pip install fastapi pydantic langgraph

# Save dependencies (like pom.xml)
pip freeze > requirements.txt
```
## 2. Advanced Setup: Poetry & uv (The True Maven Equivalents)

While `venv` and `pip` are built into Python, enterprise teams rarely use them in isolation because they don't resolve dependency conflicts well (unlike Maven/Gradle). 

In modern Python, you will likely encounter **Poetry** or **uv**.

* **Poetry:** Uses a `pyproject.toml` file (exactly like your `pom.xml`) and a `poetry.lock` file.
* **uv:** A blazing-fast alternative written in Rust that is rapidly becoming the industry standard.

```bash
# Maven: mvn dependency:tree
# Poetry:
poetry show --tree

# Maven: mvn install
# Poetry:
poetry install
```
## 3. Variables, Types & Type Hints

Java is statically typed — you must declare String name = "Alice". Python is dynamically typed, meaning you can assign any type to any variable. However, Python also supports optional type hints (introduced in Python 3.5+) which Pydantic and FastAPI both rely on heavily. Type hints don't enforce types at runtime by themselves — they are documentation + tooling support.

!!! info "Java Analogy"
    Java's "String name" tells the compiler the type. Python's "name: str" tells your IDE and tools the type. Same intent, different enforcement.

**Example — Variables with and without type hints**

```python
# Java:   String name = "Alice";  int age = 22;  boolean active = true;
# Python (no hints): name = "Alice";  age = 22;  active = True

# Python with type hints (recommended — used by FastAPI + Pydantic)
name: str = "Alice"
age: int = 22
active: bool = True
scores: list[int] = [95, 87, 92]
metadata: dict[str, str] = {"role": "admin", "tier": "pro"}

# f-strings (like String.format() in Java)
greeting = f"Hello {name}, you are {age} years old"
print(greeting)   # Hello Alice, you are 22 years old
```

| Spring Boot (Java) | Python |
|---|---|
| `int age = 25;` | `age = 25`  or  `age: int = 25` |
| `String s = "hi";` | `s = "hi"`  or  `s: str = "hi"` |
| `boolean flag = true;` | `flag = True` |
| `List<String> items = new ArrayList<>();` | `items: list[str] = []` |
| `Map<String,Object> map = new HashMap<>();` | `data: dict[str, Any] = {}` |
| `null` | `None` |
## 4. Type Checking: mypy & pyright

It is critical to understand that **Python type hints do not stop your code from running.** 
In Java, passing a String to an `int` parameter halts the compiler. In Python, it runs perfectly fine and crashes at runtime.

To get Java-like compile-time enforcement, you must run an external tool like **mypy** or **pyright** in your CI/CD pipeline or IDE. (Pydantic is the exception—it enforces types at runtime).
## 5. Enums

Python supports enums, which are crucial for status fields in APIs. They inherit from `str` and `Enum` to make them JSON serializable.

```python
from enum import Enum

class Role(str, Enum):
    ADMIN = "admin"
    TEACHER = "teacher"
    STUDENT = "student"

user_role = Role.ADMIN
print(user_role.value)  # "admin"
```
## 6. Control Flow

Python uses indentation (whitespace) to define code blocks — there are no curly braces {}. This is not optional: wrong indentation = syntax error. The standard is 4 spaces per level. PEP 8 (Python's style guide) enforces this.

!!! warning
    Python blocks are defined by indentation — 4 spaces per level. NEVER mix tabs and spaces. Use an editor like VS Code with Python extension to handle this automatically.

**Example — if/elif/else, for loops, and list comprehensions**

```python
# if/elif/else  (indentation = the block — no braces!)
score = 85
if score >= 90:
    grade = "A"
elif score >= 75:
    grade = "B"
else:
    grade = "C"

# for loop (like Java's enhanced for)
fruits = ["apple", "banana", "mango"]
for fruit in fruits:
    print(fruit)

# range (like for(int i=0; i<5; i++))
for i in range(5):      # 0, 1, 2, 3, 4
    print(i)

# List comprehension — no Java equivalent, very Pythonic
squares = [x**2 for x in range(10)]
evens   = [x for x in range(20) if x % 2 == 0]
```
## 7. Functions

Python functions are defined with the def keyword. Type hints are optional but strongly recommended. Default arguments, *args, and **kwargs make Python functions extremely flexible — features that Java only partially supports via overloading.

**Example — Functions with defaults and *args**

```python
# Basic function with type hints
def greet(name: str) -> str:
    return f"Hello, {name}!"

# Default arguments (requires overloading in Java)
def connect(host: str, port: int = 8080, secure: bool = False) -> str:
    protocol = "https" if secure else "http"
    return f"{protocol}://{host}:{port}"

connect("localhost")               # http://localhost:8080
connect("prod.api.com", 443, True) # https://prod.api.com:443

# *args and **kwargs (like Java varargs)
def log(*messages: str, level: str = "INFO"):
    for msg in messages:
        print(f"[{level}] {msg}")

# Lambda (like Java's lambda, but simpler)
double = lambda x: x * 2
add    = lambda a, b: a + b
```
## 8. Async/Await — Critical for FastAPI

Python's async/await is similar to Java's CompletableFuture, but cleaner and more widely used. FastAPI is built entirely around async — every route handler can (and should) be async def. The event loop is managed by FastAPI/uvicorn, so you don't need to manually call asyncio.run().

!!! tip
    In Spring Boot, async is something you opt into with @Async and CompletableFuture. In FastAPI, async is just how it works. Mark your route handlers as "async def" and you're already most of the way there.

**Example — async/await basics**

```python
import asyncio

# async function — returns a coroutine, not a direct value
async def fetch_data(url: str) -> dict:
    await asyncio.sleep(1)            # Non-blocking wait
    return {"url": url, "data": "..."}

# await pauses this function until fetch_data completes
async def main():
    result = await fetch_data("https://api.example.com/users")
    print(result)

    # Run multiple concurrently (like CompletableFuture.allOf)
    t1, t2 = asyncio.create_task(fetch_data("url1")), asyncio.create_task(fetch_data("url2"))
    results = await asyncio.gather(t1, t2)

asyncio.run(main())
```


## 9. Classes & OOP

Python OOP is very similar to Java — classes, constructors, inheritance, method overriding. The key difference: self is the Python equivalent of Java's this, and it must be explicitly declared as the first argument of every instance method. __init__ is the constructor. __repr__ is like toString().

**Example — Class, constructor, inheritance**

```python
class User:
    user_count: int = 0          # Class variable (static in Java)

    def __init__(self, name: str, email: str):  # Constructor
        self.name = name                         # Instance variable
        self.email = email
        User.user_count += 1

    def greet(self) -> str:
        return f"Hi, I'm {self.name}"

    def __repr__(self) -> str:                  # like toString()
        return f"User(name={self.name})"

# Inheritance (extends User)
class AdminUser(User):
    def __init__(self, name: str, email: str, role: str):
        super().__init__(name, email)            # super() call
        self.role = role

    def greet(self) -> str:                     # Override
        return f"Hi, I'm Admin {self.name} [{self.role}]"
```
## 10. Encapsulation (There is no "private")

Java enforces encapsulation at compile time using `private` and `protected`. Python has **no real private variables**. 

Instead, Python relies on a gentleman's agreement:

* `_variable`: A single underscore means "protected/internal". Please don't touch this from outside.
* `__variable`: A double underscore invokes name mangling (it renames the variable behind the scenes to prevent accidental overriding in subclasses). It is **not** security or strict privacy.

```python
class BankAccount:
    def __init__(self):
        self.public_balance = 100
        self._internal_balance = 100    # "Protected" convention
        self.__secret_balance = 100     # Name mangled

account = BankAccount()
print(account._internal_balance)      # Works fine (but you shouldn't do it)
# print(account.__secret_balance)     # AttributeError!
print(account._BankAccount__secret_balance)  # Works fine (Name mangling bypassed)
```
## 11. Abstract Classes & Interfaces

Python doesn't have a `public interface` keyword. Instead, we use the `abc` (Abstract Base Classes) module.

```python
from abc import ABC, abstractmethod

# Java: public interface PaymentProcessor { void process(int amount); }
class PaymentProcessor(ABC):
    
    @abstractmethod
    def process(self, amount: int):
        pass

class StripeProcessor(PaymentProcessor):
    def process(self, amount: int):
        print(f"Processing ${amount} via Stripe")
```
## 12. Multiple Inheritance & MRO

Java only allows single-class inheritance. Python allows true multiple inheritance. When multiple parent classes have the same method, Python uses MRO (Method Resolution Order) to decide which one to call.

```python
class A:
    def speak(self): print("A")

class B(A):
    def speak(self): print("B")

class C(A):
    def speak(self): print("C")

class D(B, C):
    pass

d = D()
d.speak()  # Prints "B" because B is listed first in D(B, C)
print(D.__mro__)  # Shows the exact resolution order: D -> B -> C -> A -> object
```
## 13. Exception Handling

Python's try/except/else/finally maps almost 1-to-1 to Java's try/catch/finally. The else block is Python-specific — it runs only if no exception was raised, which can be useful for clean flow separation.

**Spring Boot (Java)**

```java
try {
    result = riskyOp();
} catch (IOException e) {
    // handle IO error
} catch (Exception e) {
    // generic catch
    throw e;  // re-throw
} finally {
    cleanup();
}

```

**Python**

```python
try:
    result = risky_op()
except IOError as e:
    # handle IO error
    print(f"IO error: {e}")
except Exception as e:
    # generic catch-all
    raise              # re-raise
else:
    print("No errors!")
finally:
    cleanup()
```
## 14. Context Managers (try-with-resources)

In Java, you use `try-with-resources` to ensure database connections and files are closed deterministically. Python's equivalent is the `with` statement. 

!!! warning
    If you don't use `with`, your files and DB connections will stay open until the garbage collector runs, which can take a long time in Python.

```python
# Java:
# try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) { ... }

# Python:
with open("file.txt", "r") as f:
    content = f.read()
# f is automatically closed here, even if an exception occurs
```
