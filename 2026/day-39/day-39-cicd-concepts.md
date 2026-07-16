# Day 39 – What is CI/CD?

---

CI/CD is a set of practices that automate the process of integrating code changes, testing them, building applications, and deploying them to different environments. It helps teams deliver software faster, more reliably, and with fewer manual errors.


## Challenge Tasks

### Task 1: The Problem
Think about a team of 5 developers all pushing code to the same repo, manually deploying to production.

Write in your notes:
1. What can go wrong?
   * Developers may overwrite each other's changes.
   * Merge conflicts become more common.
   * Bugs may reach production because testing is inconsistent.
   * Different developers may use different software versions or environments.
   * Teams may accidentally deploy incomplete or untested code.
   
3. What does "it works on my machine" mean and why is it a real problem?
   * "It works on my machine" means that an application runs correctly on a developer's local computer but fails when run on another developer's machine, a testing environment, or a production server. This usually happens because of differences in operating systems, software versions, Config settings etc.
     
5. How many times a day can a team safely deploy manually?
   * There is no fixed number, but manual deployments are usually limited because they are slow and prone to human error. Many teams may only deploy once a day or even less frequently
   * With CI/CD automation, teams can safely deploy multiple times a day, or even hundreds of times a day, depending on their processes.

---

### Task 2: CI vs CD
Research and write short definitions (2-3 lines each):
1. **Continuous Integration** — what happens, how often, what it catches
   * Continuous Integration is a development practice where developers merge their code changes into a central repository multiple times a day. Automated builds and tests run with every merge, which helps catch integration bugs, syntax errors, and logic conflicts early in the development cycle.
   * Real-world example: A team of developers working on a mobile app pushes code to a shared repository; a CI server (like Jenkins or GitHub Actions) automatically triggers a build and runs unit tests to ensure the new code doesn't break existing functionality.
     
3. **Continuous Delivery** — how it's different from CI, what "delivery" means
  * Continuous Delivery extends CI by automatically preparing code changes for a release to production after passing all tests. Unlike CI, which focuses on code integration, "delivery" means that the software is always in a deployable state, though the actual move to production requires a manual trigger or approval.
  * Real-world example: A web development team uses automated pipelines to deploy their application to a "Staging" environment that mirrors production; once the QA team verifies the release, they click a "Deploy to Production" button to push the update live.
    
5. **Continuous Deployment** — how it differs from Delivery, when teams use it
   * Continuous Deployment is the final stage of automation where every change that passes the automated pipeline is automatically released to production without any human intervention. Teams use this when they have high confidence in their automated test suite and need to deliver features to users as quickly as possible.
   * Real-world example: A SaaS company like Netflix or Amazon uses a fully automated pipeline where, as soon as a developer’s code passes all integration, performance, and security tests, it is immediately pushed to the live production servers for all users to see.

---

### Task 3: Pipeline Anatomy
A pipeline has these parts — write what each one does:
- **Trigger** — what starts the pipeline
  * The event or condition that initiates the pipeline execution. Common triggers include pushing code to a specific branch, creating a Pull Request, a scheduled time, or a manual button press.
- **Stage** — a logical phase (build, test, deploy)
  * A logical grouping of tasks that represent a major phase in the software development lifecycle (e.g., Build, Test, or Deploy). Stages usually run in a specific sequence, and a pipeline typically waits for all jobs in one stage to complete successfully before moving to the next.
- **Job** — a unit of work inside a stage
  * A specific unit of work within a stage that is executed by a single runner. A single stage can contain multiple parallel jobs (e.g., running unit tests for different operating systems simultaneously).
- **Step** — a single command or action inside a job
  * The smallest unit of execution within a job. These are individual commands, script executions, or pre-defined actions (e.g., npm install, docker build, or running a shell script).
- **Runner** — the machine that executes the job
  * The physical or virtual machine (often a container or a dedicated server) that carries out the instructions defined in the jobs. It pulls the code and executes the steps sequentially.
- **Artifact** — output produced by a job
  * The persistent file or data produced by a job that needs to be saved after the job finishes. Examples include compiled binaries, build logs, test reports, or container images that can be passed to subsequent stages or downloaded by users.

---

### Task 4: Draw a Pipeline
Draw a CI/CD pipeline for this scenario:
> A developer pushes code to GitHub. The app is tested, built into a Docker image, and deployed to a staging server.

Include at least 3 stages. Hand-drawn and photographed is perfectly fine.

```
                Developer
                    │
                    │ Push Code
                    ▼
               GitHub Repository
                    │
                    ▼
          Trigger GitHub Actions
                    │
                    ▼
        ┌─────────────────────┐
        │ Stage 1: Build      │
        │ - Checkout Code     │
        │ - Install Packages  │
        │ - Build Application │
        └─────────────────────┘
                    │
                    ▼
        ┌─────────────────────┐
        │ Stage 2: Test       │
        │ - Unit Tests        │
        │ - Lint Code         │
        │ - Security Scan     │
        └─────────────────────┘
                    │
                    ▼
        ┌─────────────────────┐
        │ Stage 3: Deploy     │
        │ - Build Docker Image│
        │ - Push Image        │
        │ - Deploy to Staging │
        └─────────────────────┘
                    │
                    ▼
              Staging Server
```

---

### Task 5: Explore in the Wild
1. Open any popular open-source repo on GitHub (Kubernetes, React, FastAPI — pick one you know)
2. Find their `.github/workflows/` folder
3. Open one workflow YAML file
4. Write in your notes:
   - What triggers it?
   - How many jobs does it have?
   - What does it do? (best guess)

---

