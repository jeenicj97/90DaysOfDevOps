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
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32$ mkdir website
   jeenicj@DESKTOP-BG3MAVI:~/day32$ cd website
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ vim index.html
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ ls
   index.html
   ```
3. Run an Nginx container and **bind mount** your folder to the Nginx web directory
```
jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run --name mynginx3 -p 8044:80 -v /home/jeenicj/day32/website:/usr/share/nginx/html:r
o -d nginx
30c0450ea57efdbf386def687b5134cc84f52016b767a506897484b40c70005c

jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker exec -it mynginx3 ls -l /usr/share/nginx/html
total 4
-rw-r--r-- 1 1000 1000 37 Jul  6 04:21 index.html
```
4. Access the page in your browser
   > `Page accessible at http://localhost:8044`
   
6. Edit the `index.html` on your host — refresh the browser
   > `After editing & refreshing, I see the changes are immediately updated. No rebuild required.`
   
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/01a8e1459e33dfc80423990f0b5ab371853f1a7c/2026/day-32/day32-html.jpg)

Write in your notes: What is the difference between a named volume and a bind mount?

| **[Named Volume](ca://s?q=Named_volume_in_Docker)** | **[Bind Mount](ca://s?q=Bind_mount_in_Docker)** |
| --- | --- |
| Managed by Docker | Directly maps a host folder/file |
| Stored in Docker’s internal location (``/var/lib/docker/volumes/...``) | Stored wherever you specify on your host |
| Good for **persistent app data** (databases, configs) | Good for **development** (live editing, syncing files) |
| Docker controls lifecycle | You control lifecycle manually |
| More portable across environments | Tied to exact host path |


```
> A named volume is managed by Docker and is primarily used for persistent application data, such as databases.

> A bind mount maps a specific directory from the host machine into the container, making it ideal for development because changes inside the host are immediately reflected inside the container.
```
---

### Task 4: Docker Networking Basics
1. List all Docker networks on your machine
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker network ls
   NETWORK ID     NAME       DRIVER    SCOPE
   967aae37c7ec   bridge     bridge    local
   8af9aae108c0   host       host      local
   3ba3465ccedd   kind       bridge    local
   75a722e60e3e   minikube   bridge    local
   1081f413e461   none       null      local
   ```
   
3. Inspect the default `bridge` network
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker network inspect bridge
   ```
5. Run two containers on the default bridge — can they ping each other by **name**?
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run -dit --name container1 ubuntu
   a6bf17ed156cd62d1cd00c5b0ce94614ca82a2cbf0b21fc0ac757609c131bc6e
   
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run -dit --name container2 ubuntu
   0e1c9df5ef6774f5bc9c1b344f766d279e2c4e3354c56e7c4f0b20f0ba1af255

   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker exec -it container1 bash
   
   root@a6bf17ed156c:/# ping container2
   bash: ping: command not found
   
   root@a6bf17ed156c:/# apt update && apt install -y iputils-ping

   root@a6bf17ed156c:/# ping container2
   ping: container2: Temporary failure in name resolution

   > Not able to ping using name
   ```
7. Run two containers on the default bridge — can they ping each other by **IP**?

   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker exec -it container1 bash
   
   root@a6bf17ed156c:/# ping 172.17.0.7
   
   PING 172.17.0.7 (172.17.0.7) 56(84) bytes of data.
   64 bytes from 172.17.0.7: icmp_seq=1 ttl=64 time=7.67 ms
   64 bytes from 172.17.0.7: icmp_seq=2 ttl=64 time=0.837 ms
   64 bytes from 172.17.0.7: icmp_seq=3 ttl=64 time=0.124 ms
   64 bytes from 172.17.0.7: icmp_seq=4 ttl=64 time=0.200 ms
   64 bytes from 172.17.0.7: icmp_seq=5 ttl=64 time=0.124 ms
   64 bytes from 172.17.0.7: icmp_seq=6 ttl=64 time=0.200 ms
   64 bytes from 172.17.0.7: icmp_seq=7 ttl=64 time=0.081 ms
   --- 172.17.0.7 ping statistics ---
   7 packets transmitted, 7 received, 0% packet loss, time 6232ms
   rtt min/avg/max/mdev = 0.081/1.318/7.665/2.602 ms
   
   > Able to ping using IP Address (Used docker inspect to get IP Address of containers)
   
   ```

---

### Task 5: Custom Networks
1. Create a custom bridge network called `my-app-net`
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker network create my-app-net
   db445e05759a5797bdc2a287d824dd077bcf95ddb06716cf26b56492eeb90ecc
   
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker network ls
   NETWORK ID     NAME         DRIVER    SCOPE
   967aae37c7ec   bridge       bridge    local
   8af9aae108c0   host         host      local
   3ba3465ccedd   kind         bridge    local
   75a722e60e3e   minikube     bridge    local
   db445e05759a   my-app-net   bridge    local
   1081f413e461   none         null      local
   ```
3. Run two containers on `my-app-net`
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run -dit --name app1 --network my-app-net ubuntu
   0d3e7e378ba017fe19eb688c6e8d1cff9023fe1f711b54b5fe9a0ff08ea45733
   
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run -dit --name app2 --network my-app-net ubuntu
   9981778cd0f04209e6fbec3532c2384a2fffc53280b28f4f56e056fbdb09624f
   ```
5. Can they ping each other by **name** now?
   ```
   > Yes, able to ping using name

   root@0d3e7e378ba0:/# ping app2
   PING app2 (172.19.0.3) 56(84) bytes of data.
   64 bytes from app2.my-app-net (172.19.0.3): icmp_seq=1 ttl=64 time=4.95 ms
   64 bytes from app2.my-app-net (172.19.0.3): icmp_seq=2 ttl=64 time=0.133 ms
   64 bytes from app2.my-app-net (172.19.0.3): icmp_seq=3 ttl=64 time=0.142 ms
   64 bytes from app2.my-app-net (172.19.0.3): icmp_seq=4 ttl=64 time=0.117 ms
   
   --- app2 ping statistics ---
   4 packets transmitted, 4 received, 0% packet loss, time 3056ms
   rtt min/avg/max/mdev = 0.117/1.335/4.948/2.085 ms

   ```
7. Write in your notes: Why does custom networking allow name-based communication but the default bridge doesn't?
```
> User-defined bridge network includes Docker's embedded DNS service.
> When a container joins the network, Docker automatically registers app1 & app2 as DNS names(automatically resolves container names to IP addresses)

> The default bridge doesn't provide this automatic name resolution for standalone containers, so communication by container name does not work there.

KEY POINT:
> Default bridge → containers can talk by IP, but not by name.
> User-defined bridge → containers can talk by both IP and name (Docker provides built-in DNS).
```
---

### Task 6: Put It Together
1. Create a custom network
   ```
   > Create custom network
   
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker network create my-app-net2
   9c677cc20d83a2062658fb8684c367bfbaebc95babf7787c49e8f5f74f4e8f96

   > Create a Docker volume
   
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker volume create postgres-data2
   postgres-data2
   ```
3. Run a **database container** (MySQL/Postgres) on that network with a volume for data
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run -d --name postgres11 --network my-app-net2 -v postgres-data2:/var/lib/postgresql
   -e POSTGRES_PASSWORD=password postgres
   7b75157cb17d1073877d547fcb4a7428a88e9034758b74f79e15329d5d801731
   ```
5. Run an **app container** (use any image) on the same network
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker run -dit --name app3 --network my-app-net2 ubuntu
   299ece18ca21af656b162e3dcb82fba8704296901e1945f4947fd1cbb82edd06
   ```
7. Verify the app container can reach the database by container name
   ```
   > Yes, able to ping postgres11 using name from app3

   jeenicj@DESKTOP-BG3MAVI:~/day32/website$ docker exec -it app3 bash
   
   root@299ece18ca21:/# ping postgres11
   
   PING postgres11 (172.20.0.3) 56(84) bytes of data.
   64 bytes from postgres11.my-app-net2 (172.20.0.3): icmp_seq=1 ttl=64 time=29.5 ms
   64 bytes from postgres11.my-app-net2 (172.20.0.3): icmp_seq=2 ttl=64 time=0.163 ms
   64 bytes from postgres11.my-app-net2 (172.20.0.3): icmp_seq=3 ttl=64 time=0.212 ms
   64 bytes from postgres11.my-app-net2 (172.20.0.3): icmp_seq=4 ttl=64 time=0.141 ms
   64 bytes from postgres11.my-app-net2 (172.20.0.3): icmp_seq=5 ttl=64 time=0.166 ms
   --- postgres11 ping statistics ---
   5 packets transmitted, 5 received, 0% packet loss, time 4160ms
   rtt min/avg/max/mdev = 0.141/6.046/29.549/11.751 ms
   ```

---

