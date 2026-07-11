# Day 37 – Docker Revision & Cheat Sheet

## Goal
Take a **one-day pause** to consolidate everything from Days 29–36 so Docker actually sticks.

## Expected Output
- A markdown file: `docker-cheatsheet.md`
- A markdown file: `day-37-revision.md` with self-check answers

---

## Self-Assessment Checklist
Mark yourself honestly — **can do**, **shaky**, or **haven't done**:

- [ ] Run a container from Docker Hub (interactive + detached)
- [ ] List, stop, remove containers and images
- [ ] Explain image layers and how caching works
- [ ] Write a Dockerfile from scratch with FROM, RUN, COPY, WORKDIR, CMD
- [ ] Explain CMD vs ENTRYPOINT
- [ ] Build and tag a custom image
- [ ] Create and use named volumes
- [ ] Use bind mounts
- [ ] Create custom networks and connect containers
- [ ] Write a docker-compose.yml for a multi-container app
- [ ] Use environment variables and .env files in Compose
- [ ] Write a multi-stage Dockerfile
- [ ] Push an image to Docker Hub
- [ ] Use healthchecks and depends_on

---

## Quick-Fire Questions
Answer from memory, then verify:
1. What is the difference between an image and a container?
3. What happens to data inside a container when you remove it?
4. How do two containers on the same custom network communicate?
5. What does `docker compose down -v` do differently from `docker compose down`?
6. Why are multi-stage builds useful?
7. What is the difference between `COPY` and `ADD`?
8. What does `-p 8080:80` mean?
9. How do you check how much disk space Docker is using?

---

## Build Your Docker Cheat Sheet

## Container Commands

| Command                               | Description                             |
| ------------------------------------- | --------------------------------------- |
| `docker run nginx`                    | Run a container from an image           |
| `docker run -it ubuntu bash`          | Run an interactive container            |
| `docker run -d nginx`                 | Run a container in detached mode        |
| `docker ps`                           | List running containers                 |
| `docker ps -a`                        | List all containers                     |
| `docker stop <container-id>`          | Stop a container                        |
| `docker start <container-id>`         | Start a stopped container               |
| `docker restart <container-id>`       | Restart a container                     |
| `docker rm <container-id>`            | Remove a container                      |
| `docker exec -it <container-id> bash` | Open a shell inside a running container |
| `docker logs <container-id>`          | View container logs                     |
| `docker inspect <container-id>`       | View detailed container information     |

---

## Image Commands

| Command                                 | Description                       |
| --------------------------------------- | --------------------------------- |
| `docker images`                         | List local images                 |
| `docker pull nginx`                     | Download an image from Docker Hub |
| `docker build -t myapp:v1 .`            | Build an image from Dockerfile    |
| `docker tag myapp:v1 username/myapp:v1` | Tag an image                      |
| `docker push username/myapp:v1`         | Push image to Docker Hub          |
| `docker rmi <image-id>`                 | Remove an image                   |
| `docker history <image>`                | View image layers                 |

---

## Volume Commands

| Command                                  | Description           |
| ---------------------------------------- | --------------------- |
| `docker volume create myvolume`          | Create a named volume |
| `docker volume ls`                       | List volumes          |
| `docker volume inspect myvolume`         | View volume details   |
| `docker volume rm myvolume`              | Remove a volume       |
| `docker run -v myvolume:/app/data nginx` | Mount a named volume  |
| `docker run -v $(pwd):/app nginx`        | Use a bind mount      |

---

## Network Commands

| Command                                          | Description                      |
| ------------------------------------------------ | -------------------------------- |
| `docker network create mynetwork`                | Create a custom network          |
| `docker network ls`                              | List networks                    |
| `docker network inspect mynetwork`               | View network details             |
| `docker network connect mynetwork container1`    | Connect a container to a network |
| `docker network disconnect mynetwork container1` | Disconnect a container           |

---

## Docker Compose Commands

| Command                  | Description                     |
| ------------------------ | ------------------------------- |
| `docker compose up`      | Create and start services       |
| `docker compose up -d`   | Start services in detached mode |
| `docker compose down`    | Stop and remove services        |
| `docker compose ps`      | List running services           |
| `docker compose logs`    | View service logs               |
| `docker compose build`   | Build service images            |
| `docker compose restart` | Restart services                |

---

## Cleanup Commands

| Command                  | Description                 |
| ------------------------ | --------------------------- |
| `docker container prune` | Remove stopped containers   |
| `docker image prune`     | Remove unused images        |
| `docker volume prune`    | Remove unused volumes       |
| `docker network prune`   | Remove unused networks      |
| `docker system prune -a` | Remove all unused resources |
| `docker system df`       | Display Docker disk usage   |

---

## Common Dockerfile Instructions

| Instruction   | Purpose                                    |
| ------------- | ------------------------------------------ |
| `FROM`        | Specify the base image                     |
| `RUN`         | Execute commands during image build        |
| `COPY`        | Copy files from host to image              |
| `ADD`         | Copy files and support URLs/tar extraction |
| `WORKDIR`     | Set the working directory                  |
| `EXPOSE`      | Document the application's listening port  |
| `ENV`         | Define environment variables               |
| `ARG`         | Define build-time variables                |
| `CMD`         | Set the default command                    |
| `ENTRYPOINT`  | Configure the container's executable       |
| `USER`        | Specify the user to run the container      |
| `HEALTHCHECK` | Check container health                     |
| `VOLUME`      | Create a mount point for persistent data   |

---

## Useful Port Mapping

`docker run -p 8080:80 nginx`

* Host Port: `8080`
* Container Port: `80`
* Access application at: `http://localhost:8080`



---
