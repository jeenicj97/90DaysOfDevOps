# Day 45 – Docker Build & Push in GitHub Actions

---

## Challenge Tasks

### Task 1: Prepare
1. Use the app you Dockerized on Day 36 (or any simple Dockerfile)
2. Add the Dockerfile to your `github-actions-practice` repo (or create a minimal one)
3. Make sure `DOCKER_USERNAME` and `DOCKER_TOKEN` secrets are set from Day 44

---

### Task 2: Build the Docker Image in CI
Create `.github/workflows/docker-publish.yml` that:
1. Triggers on push to `main`
2. Checks out the code
3. Builds the Docker image and tags it

---

### Task 3: Push to Docker Hub
Add steps to:
1. Log in to Docker Hub using your secrets
2. Tag the image as `username/repo:latest` and also `username/repo:sha-<short-commit-hash>`
3. Push both tags

---

### Task 4: Only Push on Main
Add a condition so the push step only runs on the `main` branch — not on feature branches or PRs.

---

### Task 5: Add a Status Badge
1. Get the badge URL for your `docker-publish` workflow from the Actions tab
2. Add it to your `README.md`
3. Push — the badge should show green

[![Docker Publish](https://github.com/jeenicj97/github-actions-day44/actions/workflows/docker-publish.yml/badge.svg)](https://github.com/jeenicj97/github-actions-day44/actions/workflows/docker-publish.yml)

---

### Task 6: Pull and Run It
1. On your local machine (or a cloud server), pull the image you just pushed
2. Run it
3. Confirm it works

Write in your notes: What is the full journey from `git push` to a running container?



```
jeenicj@DESKTOP-BG3MAVI:~/day45/github-actions-day44/.github/workflows$ docker pull jeenicj97/repo:latest
latest: Pulling from jeenicj97/repo
26c307b5e35a: Already exists
dffb4b798a6d: Already exists
e757cd4ee070: Already exists
a8bc142d2ea1: Already exists
e61f92af617d: Pull complete
4dd2747d6335: Pull complete
4aa12e09b2a6: Pull complete
f88f03dac5a4: Pull complete
Digest: sha256:2d9682a7412fc9817902821ec3a05cd8e558a93fcdee2890cb6d4b5089bfb8a6
Status: Downloaded newer image for jeenicj97/repo:latest
docker.io/jeenicj97/repo:latest

jeenicj@DESKTOP-BG3MAVI:~/day45/github-actions-day44/.github/workflows$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED       STATUS                       PORTS                                                 NAMES
a532681d81ec   kindest/node:v1.35.0   "/usr/local/bin/entr…"   5 days ago    Up About an hour             0.0.0.0:30080->30080/tcp, 127.0.0.1:34603->6443/tcp   devops-cluster-control-plane
8db398b361ca   postgres:17-alpine     "docker-entrypoint.s…"   4 weeks ago   Up About an hour (healthy)   5432/tcp                                              postgres-ab
2aba402d40da   kindest/node:v1.35.0   "/usr/local/bin/entr…"   5 weeks ago   Up About an hour                                                                   tws-cluster-worker
jeenicj@DESKTOP-BG3MAVI:~/day45/github-actions-day44/.github/workflows$ docker run -p 5000:5000 jeenicj97/repo:latest
 * Serving Flask app 'app'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.17.0.2:5000

```

[Click here to view workflow](https://github.com/jeenicj97/github-actions-day44/blob/main/.github/workflows/docker-publish.yml)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-45/day45-1.jpghttps://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-45/day45-1.jpg)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-45/day45-task.jpg)


---
