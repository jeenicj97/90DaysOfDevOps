# Day 32 – Docker Volumes & Networking

---

## Challenge Tasks

### Task 1: The Problem
1. Run a Postgres or MySQL container
   ```
   docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres
   ```
3. Create some data inside it (a table, a few rows — anything)
   ```
   docker exec -it 8de38d9c5adc psql -U postgres

   postgres=# CREATE TABLE test (id SERIAL PRIMARY KEY, name TEXT);
   INSERT INTO test (name) VALUES ('Jeeni'), ('Docker');
   CREATE TABLE
   INSERT 0 2

   postgres=# SELECT * from test;
    id |  name
   ----+--------
     1 | Jeeni
     2 | Docker
   (2 rows)
   
   
   postgres=# \q
   ```
5. Stop and remove the container
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker stop 8de38d9c5adc
   8de38d9c5adc
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker rm 8de38d9c5adc
   8de38d9c5adc
   ```
7. Run a new one — is your data still there?
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker exec -it postgres1 psql -U postgres
    psql (18.4 (Debian 18.4-1.pgdg13+1))
    Type "help" for help.
    
    postgres=# SELECT * from test;
    ERROR:  relation "test" does not exist
    LINE 1: SELECT * from test;
   ```

Write what happened and why.
> `As Table was stored inside container, it was deleted when the container was removed.`

> `Any stored data inside the container is lost unless it is stored in a Docker volume or bind mount`

---

### Task 2: Named Volumes
1. Create a named volume
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker volume create postgres-data
   postgres-data

   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker volume ls
   DRIVER    VOLUME NAME
   local     postgres-data

   ```
3. Run the same database container, but this time **attach the volume** to it
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker run --name postgres2 -e POSTGRES_PASSWORD=mysecretpassword -d -v postgres-data:/var/lib/postg
   resql postgres
   01d756a7c9ace75a8e87fa2ccec89542f82b68b1fb84e2ce1e8fa85542980747
   
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker exec -it postgres2 psql -U postgres
   ```
5. Add some data, stop and remove the container
6. Run a brand new container with the **same volume**
7. Is the data still there?
   > `Yes, data persists. As data lives in the Docker-managed volume and not in the container.`

   > `Even if the container is deleted, the volume remains and so does data attached to it.`
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker stop postgres2
   postgres2
   
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker rm postgres2
   postgres2
   
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker run --name postgres2 -e POSTGRES_PASSWORD=mysecretpassword -d -v postgres-data:/var/lib/postgresql postgres
   0bdb66a985fd64a54c87fb78526fff288957e3b7f9abac7a461b5f264cae0ec6
   
   jeenicj@DESKTOP-BG3MAVI:~/day32$ docker exec -it postgres2 psql -U postgres
   psql (18.4 (Debian 18.4-1.pgdg13+1))
   Type "help" for help.
   
   postgres=# select * from test;
    id |  name
   ----+--------
     1 | Jeeni
     2 | Docker
   (2 rows)
   ```

**Verify:** `docker volume ls`, `docker volume inspect`

```
jeenicj@DESKTOP-BG3MAVI:~/day32$ docker volume ls
DRIVER    VOLUME NAME
local     postgres-data

jeenicj@DESKTOP-BG3MAVI:~/day32$ docker volume inspect postgres-data
[
    {
        "CreatedAt": "2026-07-06T03:32:01Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/postgres-data/_data",
        "Name": "postgres-data",
        "Options": null,
        "Scope": "local"
    }
]
```

---

### Task 3: Bind Mounts
1. Create a folder on your host machine with an `index.html` file
2. Run an Nginx container and **bind mount** your folder to the Nginx web directory
3. Access the page in your browser
4. Edit the `index.html` on your host — refresh the browser

Write in your notes: What is the difference between a named volume and a bind mount?

---

### Task 4: Docker Networking Basics
1. List all Docker networks on your machine
2. Inspect the default `bridge` network
3. Run two containers on the default bridge — can they ping each other by **name**?
4. Run two containers on the default bridge — can they ping each other by **IP**?

---

### Task 5: Custom Networks
1. Create a custom bridge network called `my-app-net`
2. Run two containers on `my-app-net`
3. Can they ping each other by **name** now?
4. Write in your notes: Why does custom networking allow name-based communication but the default bridge doesn't?

---

### Task 6: Put It Together
1. Create a custom network
2. Run a **database container** (MySQL/Postgres) on that network with a volume for data
3. Run an **app container** (use any image) on the same network
4. Verify the app container can reach the database by container name

---

