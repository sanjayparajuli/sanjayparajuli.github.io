---
layout: post
title: "Docker basics every developer should know"
date: 2026-06-05
tags: [DevOps]
excerpt: "Containers demystified — from your first Dockerfile to multi-stage builds."
---

Docker is one of those tools that feels confusing until it suddenly clicks. Once it does, you'll wonder how you ever shipped software without it.

## What is a container?

A container packages your app and everything it needs — code, runtime, libraries — into a single unit that runs the same way everywhere. No more "works on my machine."

## Your first Dockerfile

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Build and run it:

```bash
docker build -t my-app .
docker run -p 8000:8000 my-app
```

## Multi-stage builds

Multi-stage builds keep your final image small by separating the build environment from the runtime:

```dockerfile
# Build stage
FROM python:3.12 AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user -r requirements.txt

# Runtime stage
FROM python:3.12-slim
WORKDIR /app
COPY --from=builder /root/.local /root/.local
COPY . .
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## Docker Compose for local development

```yaml
services:
  app:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      DATABASE_URL: postgresql://user:secret@db/mydb

  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Run everything with `docker compose up`.

## Key commands to remember

- `docker ps` — list running containers
- `docker logs <container>` — view logs
- `docker exec -it <container> bash` — open a shell inside a running container
- `docker system prune` — clean up unused images and containers
