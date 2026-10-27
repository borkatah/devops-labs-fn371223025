# Service / Услугата

## Purpose / Предназначение

Notes API is a small REST service for creating and reading short text notes. It is the practice app of the DevOps course: each lab adds containers, a database, CI/CD and deployment around it.

## Endpoints / Адреси

| Method | Path | What it does / Какво прави |
|---|---|---|
| GET | /health | Liveness check: returns `{"status": "ok"}` while the process is running. |
| GET | /ready | Readiness check: pings the storage; 503 `{"status": "storage unavailable"}` if PostgreSQL cannot be reached. |
| GET | /version | Returns `{"version": "..."}` from `APP_VERSION` (default `0.1.0`). |
| GET | /model | Returns which ML model file is loaded (`MODEL_PATH`), or `null`. |
| GET | /notes | Lists all notes. |
| POST | /notes | Creates a note from `{"title": "...", "body": "..."}`; `title` is 1-100 characters; returns 201 and the note. |
| GET | /notes/{id} | Returns one note; 404 if it does not exist. |

## Dependencies / Зависимости

- Python 3.12 and the packages in `requirements.txt` (FastAPI, Uvicorn, psycopg).
- Optional: PostgreSQL through `DATABASE_URL` (Lab 4). Without it, notes are kept in memory and lost on restart.
- Optional: a trained model file through `MODEL_PATH` (Lab 10).
