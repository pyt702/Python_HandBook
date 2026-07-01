# 🚀 Getting Started

*Setting up your machine for Python AI Development.*
Learn how to install Python, configure your IDE, and run your first virtual environment.
**Estimated Reading Time:** 10 min

---

## 1. Overview

Welcome to the **Python Handbook for Spring Boot Developers**.

If you are coming from the Java and Spring Boot ecosystem, you are already familiar with building robust, scalable, and production-grade applications. You know about dependency injection, object-relational mapping (ORM), REST controllers, and bean validation. 

This handbook is designed to bridge your existing architectural knowledge with the modern Python ecosystem. We won't teach you *how* to program; instead, we will map what you already know to the tools that power today's most advanced AI and API systems.

## 2. Who is this handbook for?

This guide is written specifically for backend developers, software engineers, and architects who:

- Have experience with Java, Spring Boot, or similar enterprise frameworks.
- Understand core backend concepts like REST, CRUD, Dependency Injection, and Databases.
- Are looking to transition into Python for AI development, API building, or data engineering.
- Want a fast-tracked path that maps their existing mental models to Python equivalents without sitting through beginner tutorials.

## 3. What you'll learn

By the end of this handbook, you will be able to:

- Write idiomatic, modern Python code with strict typing.
- Build high-performance async APIs using **FastAPI**.
- Validate complex data structures using **Pydantic**.
- Design and orchestrate stateful AI workflows and agents using **LangGraph**.
- Monitor, trace, and evaluate your LLM applications using **LangFuse**.
- Combine all these tools to build a fully functional, production-ready enterprise application.

## 4. The Modern Python Stack

The Python ecosystem has evolved dramatically. Today's "State of the Art" stack for building AI-native APIs is highly opinionated, extremely fast, and heavily relies on type hints. 

Here is how the modern Python stack maps to your Spring Boot experience:

| **Spring Boot / Java** | **Modern Python Stack** | **Purpose** |
| :--- | :--- | :--- |
| **Spring Web / WebFlux** | **FastAPI** | High-performance, async-first web framework with auto-generated Swagger UI. |
| **Jackson + Bean Validation** | **Pydantic** | Strictly typed data validation, serialization, and schema definition. |
| **Spring State Machine / Flow** | **LangGraph** | Orchestrating stateful, multi-actor LLM agent workflows. |
| **Micrometer + Zipkin** | **LangFuse** | Distributed tracing and observability, specifically tailored for LLM applications. |
| **Maven / Gradle** | **pip + venv** | Dependency management and environment isolation. |
| **JPA / Hibernate** | **SQLAlchemy** | The de-facto ORM for Python. |

## 5. Prerequisites

Before diving in, you should have the following installed on your machine:

- **Python 3.10 or higher**: Modern Python features (like type hints and advanced async) require recent versions.
- **An IDE**: VS Code (with the Python extension), PyCharm, or IntelliJ IDEA.
- **Basic terminal knowledge**: For creating virtual environments and running scripts.

## 6. How to Read This Handbook

This documentation is structured progressively. We recommend reading it in order, as each chapter builds upon the last, culminating in a fully-fledged project.

* **Chapter 0 & 1:** We cover the quirks of Python syntax, data structures, and features like generators and decorators, drawing direct parallels to Java.
* **Chapter 2 (Pydantic):** You will learn how to define your data models, DTOs, and validation logic.
* **Chapter 3 (FastAPI):** We expose those Pydantic models as REST endpoints using FastAPI.
* **Chapter 4 (LangGraph):** We introduce AI workflows, teaching you how to build stateful agents capable of executing complex business logic.
* **Chapter 5 (LangFuse):** We instrument the entire stack to trace API latency, token usage, and LLM costs.

## 7. Project Structure

Throughout the handbook, we will be building **EduTrack**, a full-stack School Management System. By Chapter 6, your project structure will look something like this:

```text
edutrack/
├── main.py               # Application entry point
├── requirements.txt      # Dependency list
├── database.py           # DB connection setup
├── models/               # SQLAlchemy entities
├── schemas/              # Pydantic validation models
├── repositories/         # Database access layer
├── services/             # Business logic
├── routes/               # FastAPI controllers
└── agent/                # LangGraph AI workflows
```

## 8. What's Next?

Ready to dive in? Head over to **Python Basics** (Chapter 0) to start mapping your Java syntax knowledge directly to Python.
