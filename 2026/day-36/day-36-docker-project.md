# Day 36 – Docker Project: Dockerize a Full Application

---

## Challenge Tasks

### Task 1: Pick Your App
Choose **one** of these (or use your own project):
- A **Python Flask/Django** app with a database
- A **Node.js Express** app with MongoDB
- A **static website** served by Nginx with a backend API
- Any app from your GitHub that doesn't have Docker yet

If you don't have an app, clone a simple open-source one and Dockerize it.

---

### Task 2: Write the Dockerfile
1. Create a Dockerfile for your application
2. Use a **multi-stage build** if applicable
3. Use a **non-root user**
4. Keep the image **small** — use alpine or slim base images
5. Add a `.dockerignore` file
      
      ```
      > Architecture:
      
      Browser
         |
         v
      Flask App (Port 5000)
         |
         v
      PostgreSQL Database (Port 5432)
      
      Custom Network: app-network
      Named Volume: postgres-data

      ____________________________________________

      > Project Structure:
      
      flask-postgres-app/
      │
      ├── app/
      │   ├── app.py
      │   ├── requirements.txt
      │   └── Dockerfile
      │
      ├── docker-compose.yml
      ├── .env
      ├── .dockerignore
      └── README.md

```
> Folder Structure:

jeenicj@DESKTOP-BG3MAVI:~/day36$ ls
app  docker-compose.yml

jeenicj@DESKTOP-BG3MAVI:~/day36/app$ ls
Dockerfile  app.py  requirements.txt

--------------------------------------------------

> requirements.txt

Flask==3.1.1
psycopg2-binary==2.9.10

--------------------------------------------------

> app.py

from flask import Flask
import psycopg2
import os

app = Flask(__name__)

DB_HOST = os.getenv("DB_HOST")
DB_NAME = os.getenv("DB_NAME")
DB_USER = os.getenv("DB_USER")
DB_PASSWORD = os.getenv("DB_PASSWORD")


@app.route("/")
def home():
    return "Hello from Flask + PostgreSQL Docker Project!"


@app.route("/health")
def health():
    return "Application is running"


app.run(host="0.0.0.0", port=5000)


FROM python:3.12-slim AS builder

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir \
    --prefix=/install \
    -r requirements.txt

--------------------------------------------------

> Dockerfile

FROM python:3.12-slim

RUN useradd -m appuser

WORKDIR /app

COPY --from=builder /install /usr/local

COPY . .

USER appuser

EXPOSE 5000

CMD ["python", "app.py"]

--------------------------------------------------

> .dockerignore

__pycache__
*.pyc
.git
.env
README.md

--------------------------------------------------

> .env

POSTGRES_DB=employee_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=password
DB_HOST=db
DB_NAME=employee_db
DB_USER=postgres
DB_PASSWORD=password

--------------------------------------------------

> docker-compose.yml

services:

  app:
    build: ./app

    container_name: flask-app

    ports:
      - "5000:5000"

    env_file:
      - .env

    depends_on:
      db:
        condition: service_healthy

    networks:
      - app-network

  db:
    image: postgres:17-alpine

    container_name: postgres-ab

    restart: always

    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}

    volumes:
      - postgres-data:/var/lib/postgres

    healthcheck:
      test:
        ["CMD-SHELL", "pg_isready -U postgres"]

      interval: 10s
      timeout: 5s
      retries: 5

    networks:
      - app-network

volumes:
  postgres-data:

networks:
  app-network:


```



```
---

### Task 3: Add Docker Compose
Write a `docker-compose.yml` that includes:
1. Your **app** service (built from Dockerfile)
2. A **database** service (Postgres, MySQL, MongoDB — whatever your app needs)
3. **Volumes** for database persistence
4. A **custom network**
5. **Environment variables** for configuration (use `.env` file)
6. **Healthchecks** on the database

Run `docker compose up` and verify everything works together.

---

### Task 4: Ship It
1. Tag your app image
2. Push it to Docker Hub
3. Share the Docker Hub link
4. Write a `README.md` in your project with:
   - What the app does
   - How to run it with Docker Compose
   - Any environment variables needed

---

### Task 5: Test the Whole Flow
1. Remove all local images and containers
2. Pull from Docker Hub and run using only your compose file
3. Does it work fresh? If not — fix it until it does

---



Docker Hub Link:
https://hub.docker.com/repository/docker/jeenicj97/flask-app

Final Image Size:

IMAGE           ID             DISK USAGE   CONTENT SIZE   EXTRA
jeenicj97/flask-app:v1
                7a67a6fffe3f        132MB             0B
