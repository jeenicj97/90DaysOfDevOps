# Day 31 – Dockerfile: Build Your Own Images

---

## Challenge Tasks

### Task 1: Your First Dockerfile
1. Create a folder called `my-first-image`
2. Inside it, create a `Dockerfile` that:
   - Uses `ubuntu` as the base image
   - Installs `curl`
   - Sets a default command to print `"Hello from my custom image!"`
3. Build the image and tag it `my-ubuntu:v1`
4. Run a container from your image
> Dockerfile:
```
FROM ubuntu:latest

RUN apt-get update && apt-get install -y curl

CMD ["echo","Hello from my custom image!"]
```
> Output
```
jeenicj@DESKTOP-BG3MAVI:~/my-first-image$ docker run my-ubuntu:v1
Hello from my custom image!
```

---

### Task 2: Dockerfile Instructions
Create a new Dockerfile that uses **all** of these instructions:
- `FROM` — base image
- `RUN` — execute commands during build
- `COPY` — copy files from host to image
- `WORKDIR` — set working directory
- `EXPOSE` — document the port
- `CMD` — default command


> Dockerfile
```
#Choose the base image
FROM ubuntu:latest

#Executes commands during image build. Not when the container starts
RUN apt-get update

#Creates /app if it doesn't exist. All commands execute from here
WORKDIR /app

#Copies from host to image
COPY hello.txt .

#Does not actually publish the port. It only documents that the application intends to use port 8080. Publishing is done with docker run -p
EXPOSE 8080

#Run below command when the container starts
CMD ["cat","hello.txt"]
```

---

### Task 3: CMD vs ENTRYPOINT
1. Create an image with `CMD ["echo", "hello"]` — run it, then run it with a custom command. What happens?
   > `docker run myimage echo bye`
   > The custom command overrides CMD, so it prints bye instead

   > Note: `CMD` is a default that can be replaced at runtime
   
3. Create an image with `ENTRYPOINT ["echo"]` — run it, then run it with additional arguments. What happens?
   > `docker run myimage hello`
   > Executes echo hello -> prints hello

   > Note: `ENTRYPOINT` is always executed, and arguments you pass are appended to it.
   
5. Write in your notes: When would you use CMD vs ENTRYPOINT?
   * CMD
   
      > Use when you want a default command that users can override
      
      > Ex: CMD `["npm", "start"]` but allow users to run something else if they choose

   * ENTRYPOINT
     
     > Use when you want the container to behave like a specific executable
     
     > Ex: `ENTRYPOINT ["python3"]` so the running container feels like running Python, and arguments are passed directly.

   * Example: Often you’ll see both-Combined Usage
      ```
      ENTRYPOINT ["python3"]
      CMD ["app.py"]
      ```
   
       > `Default runs `python3 app.py`, but you can override CMD to run another script while keeping Python as the entrypoint.`
---

### Task 4: Build a Simple Web App Image
1. Create a small static HTML file (`index.html`) with any content
2. Write a Dockerfile that:
   - Uses `nginx:alpine` as base
   - Copies your `index.html` to the Nginx web directory
3. Build and tag it `my-website:v1`
4. Run it with port mapping and access it in your browser

---

### Task 5: .dockerignore
1. Create a `.dockerignore` file in one of your project folders
2. Add entries for: `node_modules`, `.git`, `*.md`, `.env`
3. Build the image — verify that ignored files are not included

---

### Task 6: Build Optimization
1. Build an image, then change one line and rebuild — notice how Docker uses **cache**
2. Reorder your Dockerfile so that frequently changing lines come **last**
3. Write in your notes: Why does layer order matter for build speed?

---



Happy Learning!
**TrainWithShubham**
