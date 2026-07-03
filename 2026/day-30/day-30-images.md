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
2. **Start** the container
3. **Pause** it and check status
4. **Unpause** it
5. **Stop** it
6. **Restart** it
7. **Kill** it
8. **Remove** it

Check `docker ps -a` after each step — observe the state changes.

---

### Task 4: Working with Running Containers
1. Run an Nginx container in detached mode
2. View its **logs**
3. View **real-time logs** (follow mode)
4. **Exec** into the container and look around the filesystem
5. Run a single command inside the container without entering it
6. **Inspect** the container — find its IP address, port mappings, and mounts

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

