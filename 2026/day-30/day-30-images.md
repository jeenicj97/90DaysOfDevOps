# Day 30 – Docker Images & Container Lifecycle

---

## Challenge Tasks

### Task 1: Docker Images
1. Pull the `nginx`, `ubuntu`, and `alpine` images from Docker Hub
   
   > `docker pull nginx`
   
3. List all images on your machine — note the sizes
   
   > `docker images`
   
5. Compare `ubuntu` vs `alpine` — why is one much smaller?
   
   > Alpine is smaller around 9MB and ubuntu:latest is aroung 100MB
   
7. Inspect an image — what information can you see?
   
   > `docker image inspect ubuntu:latest`
   
9. Remove an image you no longer need
    
    > `docker rmi alpine`

---

### Task 2: Image Layers
1. Run `docker image history nginx` — what do you see?
   ```
   jeenicj@DESKTOP-BG3MAVI:~$ docker image history gcr.io/k8s-minikube/kicbase:v0.0.49
   IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
   df72754dcb7f   5 months ago   ENTRYPOINT ["/usr/local/bin/entrypoint" "/sb…   0B        buildkit.dockerfile.v0
   <missing>      5 months ago   STOPSIGNAL SIGRTMIN+3                           0B        buildkit.dockerfile.v0
   <missing>      5 months ago   ENV container=docker                            0B        buildkit.dockerfile.v0
   <missing>      5 months ago   EXPOSE [22/tcp]                                 0B        buildkit.dockerfile.v0
   <missing>      5 months ago   COPY / / # buildkit                             1.35GB    buildkit.dockerfile.v0
   ```
3. Each line is a **layer**. Note how some layers show sizes and some show 0B
   
   > `Some instructions don't add files. Like CMD, ENTRYPOINT, EXPOSE, WORKDIR. These only change metadata. Since they don't create new filesystem data, Docker reports their size as 0B`
   
5. Write in your notes: What are layers and why does Docker use them?
   > `Docker images are built using multiple read-only layers. Each Dockerfile instruction creates a new layer. Docker stores layers separately and reuses unchanged layers when building or downloading images.`
      ```
      Benefits:
      > Faster image builds
      > Smaller downloads
      > Efficient storage
      > Layer caching
      > Easier image sharing
      ```
---

### Task 3: Container Lifecycle
Practice the full lifecycle on one container:
1. **Create** a container (without starting it)
   > `docker create --name myubuntu ubuntu:24.04` (to create container)
   > `docker ps` (to check if created)
   
3. **Start** the container
   > `docker start myubuntu` (to start)
   > `docker ps -a` (normally exits immediately as it has no long-running process)
   
   > Alternative -> `docker run -dit --name myubuntu1 ubuntu` (if exits quickly)
5. **Pause** it and check status
   > `docker pause myubuntu1` (to pause) `docker ps -a` (to check)
   
   > O/P-> `aa65e1932650   ubuntu   "/bin/bash"   About a minute ago   Up About a minute (Paused)`

7. **Unpause** it
   > `docker unpause myubuntu`
   
9. **Stop** it
    > `docker stop myubuntu1` (to stop)
    
11. **Restart** it
    > `docker restart myubuntu1`
    
13. **Kill** it
    > `docker kill myubuntu1`
    
15. **Remove** it
    > `docker remove myubuntu1` (container disappears if you check with docker ps -a)

Check `docker ps -a` after each step — observe the state changes.

```
| Command | State   |
| ------- | ------- |
| create  | Created |
| start   | Running |
| pause   | Paused  |
| unpause | Running |
| stop    | Exited  |
| restart | Running |
| kill    | Exited  |
| rm      | Removed |

```

---

### Task 4: Working with Running Containers
1. Run an Nginx container in detached mode
   > `docker run -d --name webserver -p 8080:80 nginx` (-d -> detached mode)
   
3. View its **logs**
   > `docker logs webserver` (you will see logs)
        
5. View **real-time logs** (follow mode)
   > `docker logs -f webserver`
   
7. **Exec** into the container and look around the filesystem
   > `docker exec -it webserver` (you can use sh or bash)
   
9. Run a single command inside the container without entering it
    > `docker exec webserver ls /` (will list all files in the webserver's root folder)
    
11. **Inspect** the container — find its IP address, port mappings, and mount
    > `docker inspect webserver`

    > `docker inspect webserver | grep Mounts` (this gives you results of Mount)

---

### Task 5: Cleanup
1. Stop all running containers in one command
2. Remove all stopped containers in one command
3. Remove unused images
4. Check how much disk space Docker is using

---

## Hints
- Image history: `docker image history`
- Create without starting: `docker create`
- Follow logs: `docker logs -f`
- Inspect: `docker inspect`
- Cleanup: `docker system df`, `docker system prune`

---

