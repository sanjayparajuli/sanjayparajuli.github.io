---
layout: post
title: "Building a REST API with FastAPI and PostgreSQL"
date: 2026-06-12
tags: [Python]
excerpt: "A step-by-step guide to setting up a production-ready API with async support, database migrations, and automatic docs."
---

FastAPI is one of the fastest-growing Python web frameworks, and for good reason. It gives you automatic OpenAPI docs, async support out of the box, and a type system that catches bugs before your tests do.

## Prerequisites

You'll need Python 3.10+, `pip`, and a running PostgreSQL instance. Spin one up with Docker:

```bash
docker run -d \
  --name my-postgres \
  -e POSTGRES_PASSWORD=secret \
  -p 5432:5432 \
  postgres:16
```

Then install the dependencies:

```bash
pip install fastapi uvicorn sqlalchemy asyncpg alembic
```

## Project structure

```
myapi/
├── main.py        # FastAPI entry point
├── database.py    # DB engine and session
├── models.py      # SQLAlchemy models
├── schemas.py     # Pydantic schemas
└── routers/
    └── items.py   # Route handlers
```

## Setting up the database

```python
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import declarative_base, sessionmaker

DATABASE_URL = "postgresql+asyncpg://user:secret@localhost/mydb"

engine = create_async_engine(DATABASE_URL, echo=True)
AsyncSessionLocal = sessionmaker(engine, class_=AsyncSession, expire_on_commit=False)
Base = declarative_base()

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```

## Creating your first route

```python
from fastapi import FastAPI

app = FastAPI(title="My API", version="1.0.0")

@app.get("/health")
async def health_check():
    return {"status": "ok"}
```

Run with `uvicorn main:app --reload` and visit `http://localhost:8000/docs` for interactive API docs.

## Next steps

- Add **Alembic** for database migrations
- Set up **JWT authentication**
- Write tests with `pytest` and `httpx`
- Deploy with Docker and Caddy
