# 📚 Chapter 1: Core Python

*Advanced Python concepts.*
Deep dive into decorators, generators, and context managers.
**Estimated Reading Time:** 20 min

---

## 1. Data Structures — List, Tuple, Set, Dict

Python has four built-in collection types. Choosing the right one matters for both correctness and performance:

| Python Type | Java Equivalent | Key Property |
|---|---|---|
| list | ArrayList<T> | Ordered, mutable |
| tuple | ImmutableList / record | Ordered, immutable |
| set | HashSet<T> | Unordered, unique values |
| dict | HashMap<K,V> | Key-value pairs |

**Example — All four collection types**

```
# List — ordered, mutable (ArrayList<T>)
items: list[str] = ["a", "b", "c"]
items.append("d")       # add to end
items.insert(0, "z")    # insert at index
items.remove("b")       # remove first match
sorted_copy = sorted(items, reverse=True)

# Tuple — ordered, IMMUTABLE (use for fixed data like coordinates)
point = (3, 5)
x, y = point            # unpacking

# Set — unordered, unique (HashSet<T>)
tags: set[str] = {"python", "backend", "api"}
tags.add("fastapi")     # add element
union_set = tags | {"rest", "python"}   # union
intersect = tags & {"python", "go"}     # intersection

# Dict — key-value (HashMap<K,V>)
user: dict[str, any] = {"id": 1, "name": "Alice", "active": True}
user["email"] = "alice@example.com"      # add/update
name = user.get("name", "Unknown")       # safe get with default
user.pop("active", None)                 # safe delete
```

#### Slicing — Python's Secret Weapon

Python's slicing syntax [start:stop:step] works on lists, strings, and tuples. There's no equivalent in Java without writing loops.

**Example — Slicing syntax**

```
data = [10, 20, 30, 40, 50, 60, 70]

data[1:4]    # [20, 30, 40]       — index 1 up to (not including) 4
data[:3]     # [10, 20, 30]       — first 3
data[-3:]    # [50, 60, 70]       — last 3
data[::2]    # [10, 30, 50, 70]   — every 2nd element
data[::-1]   # [70,60,50,40,30,20,10] — reversed

# Works on strings too!
s = "HelloWorld"
s[:5]        # "Hello"
s[-5:]       # "World"
s[::-1]      # "dlroWolleH"
```

## 2. Decorators — Python's @Annotations

Decorators are Python's equivalent of Java annotations like @Transactional, @Cacheable, @Retry. The key difference: Python decorators are actual functions that wrap other functions — there's no compiler magic. This means you can write your own decorator in 10 lines, and you'll instantly understand what Spring was doing under the hood.

!!! info "Java Analogy"
    Java @Transactional wraps your method in a transaction — Spring does it via AOP. Python @timer wraps your function with timing logic — you write the wrapper yourself. Same pattern, full transparency.

**Example — Custom timing decorator (like @Timed in Micrometer)**

```python
import time
from functools import wraps

def timer(func):
    @wraps(func)        # Preserves original function metadata (name, docstring)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)   # Call original function
        elapsed = time.time() - start
        print(f"{func.__name__} took {elapsed:.4f}s")
        return result
    return wrapper

@timer                  # Same as: process_data = timer(process_data)
def process_data(n: int):
    return sum(range(n))

process_data(1_000_000)   # process_data took 0.0321s
```

## 3. Generators — Memory-Efficient Processing

A generator produces values one at a time using the yield keyword — it doesn't build the entire collection in memory. Think of it like Java Streams, but even lazier. Generators are ideal for processing large files, paginated APIs, or infinite sequences.

**Example — Fibonacci generator and large file processing**

```python
def fibonacci():
    a, b = 0, 1
    while True:
        yield a          # suspends here, returns value
        a, b = b, a + b

fib = fibonacci()
print(next(fib))  # 0
print(next(fib))  # 1
print(next(fib))  # 1

# Generator expression (lazy list comprehension — no memory spike)
large_sum = sum(x**2 for x in range(10_000_000))

# Process huge files line by line
def read_large_file(path: str):
    with open(path) as f:
        for line in f:
            yield line.strip()   # one line in memory at a time
```

## 4. Python Best Practices & Naming Conventions

Python has PEP 8 — the official style guide, equivalent to Google's Java Style Guide. Following it is not optional in professional Python code. Most teams enforce it with Black (auto-formatter) or Ruff.

| Convention | Java | Python |
|---|---|---|
| Variables & methods | camelCase | snake_case |
| Classes | PascalCase | PascalCase |
| Constants | UPPER_SNAKE_CASE | UPPER_SNAKE_CASE |
| Private (convention) | private keyword | _single_underscore |
| Special methods | N/A | __double_underscore__ |



## 5. The Main Entrypoint

In Java, every program starts with `public static void main(String[] args)`. Python scripts execute from top to bottom. If you want a specific block of code to run only when the file is executed directly (and not when it is imported by another file), you use this idiom:

```python
def main():
    print("Application starting...")

if __name__ == "__main__":
    main()
```
Think of `if __name__ == "__main__":` as the exact equivalent to Java's `main` method.

## 6. Packages and Imports

Java uses a rigid directory structure tied to the `package` keyword. In Python, directories *are* packages. 

To make a directory a package, you place an `__init__.py` file inside it (though this is optional in Python 3.3+, it is heavily recommended for enterprise codebases).

!!! warning
    Spring Boot's IoC container handles circular dependencies easily. Python **does not**. If `file_a.py` imports `file_b.py`, and `file_b.py` imports `file_a.py`, your application will crash instantly with an `ImportError`. Always keep your imports uni-directional.

## 7. Data Classes (Lombok's @Data equivalent)

If you use Lombok's `@Data` or `@Value` to generate getters, setters, constructors, and `equals()` methods, Python has a built-in equivalent: `@dataclass`.

```python
from dataclasses import dataclass

# Java: @Data public class Point { private int x; private int y; }

@dataclass
class Point:
    x: int
    y: int

p1 = Point(10, 20)
print(p1.x)         # 10
print(p1 == Point(10, 20))  # True
```
*Note: Pydantic models (Chapter 2) are essentially "dataclasses on steroids" with built-in runtime validation.*

## 8. Java Streams vs Python Comprehensions & Itertools

Java 8 introduced the Stream API. Python handles list manipulation primarily through comprehensions, but for advanced operations, you'll need the `itertools` and `functools` libraries.

**Filtering and Mapping (Comprehensions)**
```python
data = [1, 2, 3, 4, 5]

# Java: data.stream().filter(x -> x % 2 != 0).map(x -> x * 2).toList()
result = [x * 2 for x in data if x % 2 != 0]
```

**Reducing (functools)**
```python
from functools import reduce

# Java: data.stream().reduce(0, Integer::sum)
total = reduce(lambda a, b: a + b, data, 0)
```

**Grouping (itertools)**
```python
from itertools import groupby

# Python's groupby requires the data to be sorted first!
students = [("A", "Alice"), ("B", "Bob"), ("A", "Arjun")]
students.sort(key=lambda x: x[0])

# Java: students.stream().collect(Collectors.groupingBy(s -> s.grade))
grouped = {k: list(v) for k, v in groupby(students, key=lambda x: x[0])}
```
