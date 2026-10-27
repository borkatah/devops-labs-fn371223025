# Runbook

## Start locally / Локално стартиране

~~~bash
pip install -r requirements-dev.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000
~~~

## Check that it works / Проверка, че работи

~~~bash
curl -fsS http://127.0.0.1:8000/health
~~~

You should see `{"status":"ok"}`. The API docs are at http://127.0.0.1:8000/docs.

## Run the tests / Тестове

~~~bash
pytest
ruff check .
~~~

## When something breaks / Когато нещо се счупи

1. Is the process alive? `curl -s http://127.0.0.1:8000/health`
2. Can it reach its storage? `curl -s http://127.0.0.1:8000/ready` (503 means PostgreSQL is unreachable: check `echo $DATABASE_URL`)
3. What does the log say? Read the uvicorn output in the terminal (in a container: `docker logs <container>`).

## Run with Docker / Стартиране с Docker

~~~bash
docker build -t notes-api:0.3.0 .
docker run -d --name notes-api -p 8000:8000 notes-api:0.3.0
curl -s http://127.0.0.1:8000/health
docker inspect --format '{{.State.Health.Status}}' notes-api
docker logs -f notes-api
docker stop notes-api && docker rm notes-api
~~~

`healthy` after about 10 seconds means the HEALTHCHECK passed. `docker logs -f` follows the log; Ctrl+C stops following, not the container.
