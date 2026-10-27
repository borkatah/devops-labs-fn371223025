# 0002. Database for Lab 4

**Status / Статус:** Accepted

## Context / Контекст

Notes are kept in memory and disappear on every restart. From Lab 4 the service must keep them, and it runs next to its database with Docker Compose.

## Options / Варианти

- **PostgreSQL in its own container.** Pro: the app already supports it through `DATABASE_URL` (psycopg); same engine as in production. Con: one more container to run and watch.
- **SQLite file.** Pro: no server to run. Con: needs new code in the app; a local file teaches nothing about services talking over a network.

## Decision / Решение

PostgreSQL in its own container, started by Docker Compose next to the app. The app connects with `DATABASE_URL=postgresql://notes:secret@db:5432/notes`; the data lives in a named volume.

## Consequences / Последствия

Notes survive restarts. `/ready` returns 503 while the database is down. The password is passed as an environment variable, never baked into the image.
