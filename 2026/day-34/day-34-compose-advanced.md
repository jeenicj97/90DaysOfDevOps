# Day 34 – Docker Compose: Real-World Multi-Container Apps

---

## Challenge Tasks

### Task 1: Build Your Own App Stack
Create a `docker-compose.yml` for a 3-service stack:
- A **web app** (use Python Flask, Node.js, or any language you know)
- A **database** (Postgres or MySQL)
- A **cache** (Redis)

  ```

  project/
  │
  ├── docker-compose.yml
  │
  └── app/
      ├── Dockerfile
      ├── requirements.txt
      └── app.py

  

   jeenicj@DESKTOP-BG3MAVI:~/docker-practice/app$ cat requirements.txt
    Flask
    psycopg2-binary
    redis
  

  jeenicj@DESKTOP-BG3MAVI:~/docker-practice/app$ cat app.py
  from flask import Flask
  
  app = Flask(__name__)
  
  @app.route("/")
  def home():
      return "Hello from Flask Docker App!"
  
  app.run(host="0.0.0.0", port=5000)

  

  jeenicj@DESKTOP-BG3MAVI:~/docker-practice/app$ cat Dockerfile
  
  FROM python:3.12-slim
  
  WORKDIR /app
  
  COPY requirements.txt .
  
  RUN pip install -r requirements.txt
  
  COPY . .
  
  CMD ["python","app.py"]

  
      
  jeenicj@DESKTOP-BG3MAVI:~/docker-practice$ cat docker-compose.yml

  services:
    web:
      build: ./app
      ports:
        - "5000:5000"
      depends_on:
        - db
   
    db:
      image: postgres
      environment:
        POSTGRES_PASSWORD: password
        POSTGRES_USER: postgres
        POSTGRES_DB: mydb
  
    redis:
      image: redis

 
  ```

Write a simple Dockerfile for the web app. The app doesn't need to be complex — even a "Hello World" that connects to the database is enough.

![Image Alt]()

---

### Task 2: depends_on & Healthchecks
1. Add `depends_on` to your compose file so the app starts **after** the database
2. Add a **healthcheck** on the database service
3. Use `depends_on` with `condition: service_healthy` so the app waits for the database to be truly ready, not just started

  ```
  jeenicj@DESKTOP-BG3MAVI:~/docker-practice$ cat docker-compose.yml
  services:
    web:
      build: ./app
      ports:
        - "5000:5000"
      depends_on:
        db:
          condition: service_healthy
  
    db:
      image: postgres
      environment:
        POSTGRES_PASSWORD: password
        POSTGRES_USER: postgres
        POSTGRES_DB: mydb
      healthcheck:
        test: ["CMD-SHELL", "pg_isready -U postgres"]
        interval: 10s
        timeout: 5s
        retries: 5
  
    redis:
      image: redis
  ```

**Test:** Bring everything down and up — does the app wait for the DB?

  ```
  jeenicj@DESKTOP-BG3MAVI:~/docker-practice$ docker compose up -d
  [+] Running 3/3
   ✔ Container docker-practice-db-1     Healthy               49.0s
   ✔ Container docker-practice-redis-1  Started               11.4s
   ✔ Container docker-practice-web-1    Started                2.6s
  ```

---

### Task 3: Restart Policies
1. Add `restart: always` to your database service
2. Manually kill the database container — does it come back?
3. Try `restart: on-failure` — how is it different?
4. Write in your notes: When would you use each restart policy?

---

### Task 4: Custom Dockerfiles in Compose
1. Instead of using a pre-built image for your app, use `build:` in your compose file to build from a Dockerfile
2. Make a code change in your app
3. Rebuild and restart with one command

---

### Task 5: Named Networks & Volumes
1. Define **explicit networks** in your compose file instead of relying on the default
2. Define **named volumes** for database data
3. Add **labels** to your services for better organization

---

## Learn in Public
Share your 3-service app stack running via Compose on LinkedIn.

`#90DaysOfDevOps` `#DevOpsKaJosh` `#TrainWithShubham`

Happy Learning!
**TrainWithShubham**
