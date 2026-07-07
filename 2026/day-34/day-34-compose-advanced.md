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

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/73b1c493d45672e3e120b300e45f1f258c1d6d9e/2026/day-34/day34.jpg)

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

```
> added below in the docker-compose.yml

 db:
    image: postgres
    restart: always
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_USER: postgres
      POSTGRES_DB: mydb
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

```
3. Manually kill the database container — does it come back?
4. Try `restart: on-failure` — how is it different?
5. Write in your notes: When would you use each restart policy?
   
```
   > restart: always
  - Restarts the container no matter why it stopped (manual kill, crash, etc.).
  - Use case: Production services that must always be available (e.g., databases, web servers).
  
   > restart: on-failure
  - Restarts the container only if it exits with an error (non‑zero exit code).
  - Use case: Batch jobs or scripts where retries make sense only on errors, not when stopped intentionally.

```

---

### Task 4: Custom Dockerfiles in Compose
1. Instead of using a pre-built image for your app, use `build:` in your compose file to build from a Dockerfile

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
  ```
  
  3. Make a code change in your app
  
  4. Rebuild and restart with one command
     
      > $ docker compose up --build


---

### Task 5: Named Networks & Volumes
1. Define **explicit networks** in your compose file instead of relying on the default
2. Define **named volumes** for database data
3. Add **labels** to your services for better organization

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
    networks:
      - backend
    labels:
      project: training

  db:
    image: postgres
    restart: always
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_USER: postgres
      POSTGRES_DB: mydb
    volumes:
      - postgres-data:/var/lib/postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - backend

  redis:
    image: redis

    networks:
      - backend

networks:
  backend:

volumes:
   postgres-data:
  ```

### Task 6: Scaling (Bonus)
1. Try scaling your web app to 3 replicas using `docker compose up --scale`
2. What happens? What breaks?
3. Write in your notes: Why doesn't simple scaling work with port mapping?

```
jeenicj@DESKTOP-BG3MAVI:~/docker-practice$ docker compose up --scale web=3
[+] Running 5/5
 ✔ Container docker-practice-redis-1  Running                     0.0s
 ✔ Container docker-practice-db-1     Running                     0.0s
 ✔ Container docker-practice-web-1    Running                     0.0s
 ✔ Container docker-practice-web-3    Created                     0.3s
 ✔ Container docker-practice-web-2    Created                     0.3s
Attaching to db-1, redis-1, web-1, web-2, web-3
Error response from daemon: failed to set up container networking: driver failed programming external connectivity on endpoint docker-practice-web-3 (94bab28d56a3b6c05ad118ba1d432daf2011aa2a7c4c18f245cc0963b1724b92): Bind for 0.0.0.0:5000 failed: port is already allocated
```

- Ran `docker compose up --scale web=3 -d`.
- Observed that only one replica could bind to port 5000; others failed due to port conflict.
- Simple scaling doesn’t work with direct port mapping because:
  - A host port can only be bound by one container.
  - Compose doesn’t provide built‑in load balancing.
- To scale properly:
  - Use an internal network and expose containers without host port mapping.
  - Add a reverse proxy/load balancer (e.g., Nginx, Traefik).
  - Or use orchestration tools like Docker Swarm or Kubernetes.

---

