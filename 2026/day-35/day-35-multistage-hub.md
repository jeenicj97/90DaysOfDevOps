# Day 35 – Multi-Stage Builds & Docker Hub

---

## Challenge Tasks

### Task 1: The Problem with Large Images
1. Write a simple Go, Java, or Node.js app (even a "Hello World" is fine)
   
   ```
   hello-node/
   │
   ├── app.js
   ├── package.json
   └── Dockerfile
   ```
3. Create a Dockerfile that builds and runs it in a **single stage**
   
  ```
FROM node:22

WORKDIR /app

COPY . .

CMD ["node", "app.js"]
   ```
4. Build the image and check its **size**

Note down the size — you'll compare it later.

   ```
   REPOSITORY      TAG      SIZE
   hello-node      v1       1.1GB
   ```

---

### Task 2: Multi-Stage Build
1. Rewrite the Dockerfile using **multi-stage build**:
   - Stage 1: Build the app (install dependencies, compile)
   - Stage 2: Copy only the built artifact into a minimal base image (`alpine`, `distroless`, or `scratch`)
     
     ```
     jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ cat Dockerfile
      # Stage 1
      FROM node:22 AS builder
      
      WORKDIR /app
      
      COPY . .
      
      # Stage 2
      FROM alpine:3.22
      
      RUN apk add --no-cache nodejs
      
      WORKDIR /app
      
      COPY --from=builder /app .
      
      CMD ["node", "app.js"]
     ```
2. Build the image and check its size again
3. Compare the two sizes

   ```
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker images
                                               
   IMAGE                ID             DISK USAGE   CONTENT SIZE   EXTRA
   hello-node:v2        aeb9518846d4       80.2MB             0B
   node:22              177e8051ee1a       1.13GB             0B
   ```

Write in your notes: Why is the multi-stage image so much smaller?

   ```

   Because the final image contains only:

   Application files
   Node runtime
   Minimal operating system
   
   It does not include:
   
   Build tools
   Package managers
   Compilers
   Development dependencies
   Temporary files

  > Multi-stage build creates separate build and runtime stages. Only the required artifacts are copied into the final image, which significantly reduces image size, improves security and decreases the ttack surface
   ```

---

### Task 3: Push to Docker Hub
1. Create a free account on [Docker Hub](https://hub.docker.com) (if you don't have one)
2. Log in from your terminal
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$  docker login
   
   Authenticating with existing credentials... [Username: jeenicj97]
   
   i Info → To login with a different account, run 'docker logout' followed by 'docker login'
   
   Login Succeeded
   ```
   
4. Tag your image properly: `yourusername/image-name:tag`
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker tag hello-node:v2 jeenicj97/hello-node:v2
   ```
   
6. Push it to Docker Hub
   
   ```
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker push jeenicj97/hello-node:v2

   The push refers to repository [docker.io/jeenicj97/hello-node]
   b9fabb373d98: Pushed
   5b0ee24e9337: Pushed
   f2417344d475: Pushed
   6f09edfb3f6d: Pushed
   v2: digest: sha256:4bdc7da2c31de2988ff635626949cec8de19e02b59966df19fdd2a53109d28af size: 1153
   ```
8. Pull it on a different machine (or after removing locally) to verify

   ```
   #Removing image
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker rmi jeenicj97/hello-node:v2
   Untagged: jeenicj97/hello-node:v2
   Untagged: jeenicj97/hello-node@sha256:4bdc7da2c31de2988ff635626949cec8de19e02b59966df19fdd2a53109d28af

   #Pulling image from registry
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker pull jeenicj97/hello-node:v2
   v2: Pulling from jeenicj97/hello-node
   Digest: sha256:4bdc7da2c31de2988ff635626949cec8de19e02b59966df19fdd2a53109d28af
   Status: Downloaded newer image for jeenicj97/hello-node:v2
   docker.io/jeenicj97/hello-node:v2
   ```

![Image Alt]()

---

### Task 4: Docker Hub Repository
1. Go to Docker Hub and check your pushed image
2. Add a **description** to the repository
   ![Image Alt]()
4. Explore the **tags** tab — understand how versioning works
5. Pull a specific tag vs `latest` — what happens?
   
   ```
   > It doesn't pull in latest/v1 version unless that version is pushed to registry.
   
   jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker pull jeenicj97/hello-node:latest
   Error response from daemon: manifest for jeenicj97/hello-node:latest not found: manifest unknown: manifest unknown


  > latest does not mean newest.
      
      It simply means:
      
      The image that currently has the tag named latest.
   ```

---

### Task 5: Image Best Practices
Apply these to one of your images and rebuild:
1. Use a **minimal base image** (alpine vs ubuntu — compare sizes)
2. **Don't run as root** — add a non-root USER in your Dockerfile
3. Combine `RUN` commands to **reduce layers**
4. Use **specific tags** for base images (not `latest`)

   ```
      jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ cat Dockerfile
      FROM node:22-alpine
      
      RUN addgroup appgroup && \
          adduser -D -G appgroup appuser
      
      WORKDIR /app
      
      COPY . .
      
      USER appuser
      
      CMD ["node", "app,js"]


      jeenicj@DESKTOP-BG3MAVI:~/day35/hello-node$ docker build -t hello-nolo-node:v3 .

```


---

