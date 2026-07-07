# Day 33 – Docker Compose: Multi-Container Basics

---

## Challenge Tasks

### Task 1: Install & Verify
1. Check if Docker Compose is available on your machine
2. Verify the version
   
```
jeenicj@DESKTOP-BG3MAVI:~$ docker compose version
Docker Compose version v2.40.3-desktop.1
```
---

### Task 2: Your First Compose File
1. Create a folder `compose-basics`
2. Write a `docker-compose.yml` that runs a single **Nginx** container with port mapping
   
   ```
   services:
    nginx:
      image: nginx
      container_name: my-nginx
      ports:
        - "8080:80"
   ```
4. Start it with `docker compose up`
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose up -d
    [+] Running 2/2
     ✔ Network compose-basics_default  Created                      0.5s
     ✔ Container my-nginx              Star...                      5.0s
   ```
6. Access it in your browser
   
   ![Image Alt]()
8. Stop it with `docker compose down`
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose down
    [+] Running 2/2
     ✔ Container my-nginx              Remo...                      0.1s
     ✔ Network compose-basics_default  Removed                      0.4s
   ```

---

### Task 3: Two-Container Setup
Write a `docker-compose.yml` that runs:
- A **WordPress** container
- A **MySQL** container

They should:
- Be on the same network (Compose does this automatically)
- MySQL should have a named volume for data persistence
- WordPress should connect to MySQL using the service name

Start it, access WordPress in your browser, and set it up.

```
jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ cat docker-compose.yml
services:
  db:
    image: mysql:8.0
    container_name: wordpress-db
    restart: always

    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: rootpassword

    volumes:
      - mysql_data:/var/lib/mysql

    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      retries: 5

  wordpress:
    image: wordpress:latest
    container_name: wordpress-app
    restart: always

    ports:
      - "8099:80"

    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: password
      WORDPRESS_DB_NAME: wordpress

    depends_on:
      - db

volumes:
  mysql_data:
```

**Verify:** Stop and restart with `docker compose down` and `docker compose up` — is your WordPress data still there?
```
NOT UNDERSTOOD, NEED TO CHECK
```

---

### Task 4: Compose Commands
Practice and document these:
1. Start services in **detached mode**
   
   ```
   docker compose up -d
   ```
3. View running services
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose ps

      NAME            IMAGE              COMMAND                  SERVICE     CREATED         STATUS                   PORTS
      wordpress-app   wordpress:latest   "docker-entrypoint.s…"   wordpress   9 minutes ago   Up 8 minutes             0.0.0.0:8099->80/tcp, [::]:8099->80/tcp
      wordpress-db    mysql:8.0          "docker-entrypoint.s…"   db          9 minutes ago   Up 8 minutes (healthy)   3306/tcp, 33060/tcp
   ```

5. View **logs** of all services
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose logs
   
   wordpress-app  | WordPress not found in /var/www/html - copying now...
   wordpress-app  | Complete! WordPress has been successfully copied to /var/www/html
   ```

6. View logs of a **specific** service
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose logs db
   ```

7. **Stop** services without removing
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose stop
   
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose start
   ```
   
8. **Remove** everything (containers, networks)

   ```
   $docker compose down
   
   The named volume remains unless you run:
   $docker compose down -v
   
   ```

9. **Rebuild** images if you make a change

   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker compose up --build -d
   ```

---

### Task 5: Environment Variables
1. Add environment variables directly in your `docker-compose.yml`

   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ cat docker-compose.yml
   services:
     db:
       image: mysql:8.0
       container_name: wordpress-db
       restart: always
   
       environment:
         MYSQL_DATABASE: ${MYSQ_DATABASE}
         MYSQL_USER: ${MYSQL_USER}
         MYSQL_PASSWORD: ${MYSQL_PASSWORD}
         MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
   
       volumes:
         - mysql_data:/var/lib/mysql
   
       healthcheck:
         test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
         interval: 10s
         retries: 5
   
     wordpress:
       image: wordpress:latest
       container_name: wordpress-app
       restart: always
   
       ports:
         - "8099:80"
   
       environment:
         WORDPRESS_DB_HOST: db
         WORDPRESS_DB_USER: wpuser
         WORDPRESS_DB_PASSWORD: password
         WORDPRESS_DB_NAME: wordpress
   
       depends_on:
         - db
   
   volumes:
     mysql_data:
   ```
   
3. Create a `.env` file and reference variables from it in your compose file
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ cat .env
   MYSQL_ROOT_PASSWORD=root123
   MYSQL_DATABASE=wordpress
   MYSQL_USER=jeeni
   MYSQL_PASSWORD=secret123
   ```
4. Verify the variables are being picked up
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day33/compose-basics$ docker exec -it wordpress-db env
   
   PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
   HOSTNAME=3ac84da989b4
   TERM=xterm
   MYSQL_PASSWORD=secret123
   MYSQL_ROOT_PASSWORD=root123
   MYSQL_DATABASE=
   MYSQL_USER=jeeni
   GOSU_VERSION=1.19
   MYSQL_MAJOR=8.0
   MYSQL_VERSION=8.0.46-1.el9
   MYSQL_SHELL_VERSION=8.0.46-1.el9
   HOME=/root
   ```

---

