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

## Run with Compose / Стартиране с Compose

~~~bash
cp .env.example .env            # then set a real password in .env
docker compose up -d --build    # app + PostgreSQL; the app waits until db is healthy
docker compose ps               # db should be (healthy)
docker compose logs -f app      # Ctrl+C stops following, not the app
docker compose down             # stops and removes the containers, keeps the data
docker compose down -v          # also deletes the pgdata volume = all notes
docker compose --profile tools up -d   # adds Adminer on port 8081
~~~

`down` keeps the named volume, so notes survive a restart. `down -v` removes it, and the next `up` starts with an empty database.

## Release and rollback / Издаване и връщане назад

~~~bash
docker compose -f compose.prod.yaml pull && docker compose -f compose.prod.yaml up -d   # latest from main
git tag v0.6.0 && git push origin v0.6.0                                               # CI pushes :v0.6.0
APP_TAG=v0.6.0 docker compose -f compose.prod.yaml up -d                               # an exact version
APP_TAG=<sha of a known good commit> docker compose -f compose.prod.yaml up -d         # rollback
~~~
