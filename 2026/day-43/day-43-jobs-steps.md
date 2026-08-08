# Day 43 – Jobs, Steps, Env Vars & Conditionals

---

## Challenge Tasks

### Task 1: Multi-Job Workflow
Create `.github/workflows/multi-job.yml` with 3 jobs:
- `build` — prints "Building the app"
- `test` — prints "Running tests"
- `deploy` — prints "Deploying"

Make `test` run only **after** `build` succeeds.
Make `deploy` run only **after** `test` succeeds.

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-43/day43-task1.jpg)

[Click here to view workflow file](https://github.com/jeenicj97/github-actions-day43/blob/d22ed971a22041d6103a3e5bf374aa4e4badebe0/.github/workflows/multi-job.yml)

---

### Task 2: Environment Variables
In a new workflow, use environment variables at 3 levels:
1. **Workflow level** — `APP_NAME: myapp`
2. **Job level** — `ENVIRONMENT: staging`
3. **Step level** — `VERSION: 1.0.0`

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-43/day43-task2.jpg)

[Click here to view workflow file](https://github.com/jeenicj97/github-actions-day43/blob/34c1986cbd13fa344ce847fb3a7b931ca7412d5a/.github/workflows/env-vars.yml)

---

### Task 3: Job Outputs
1. Create a job that **sets an output** — e.g., today's date as a string
2. Create a second job that **reads that output** and prints it
3. Pass the value using `outputs:` and `needs.<job>.outputs.<name>`

#### Notes: Why Pass Outputs Between Jobs?

- **[Share data](ca://s?q=Share_data_between_jobs)**  
  Outputs let one job produce information (like a date, build number, or artifact path) that another job can reuse.

- **[Avoid repetition](ca://s?q=Avoid_repetition_in_jobs)**  
  Instead of recalculating the same value in multiple jobs, you compute it once and pass it along.

- **[Coordinate jobs](ca://s?q=Coordinate_jobs_with_outputs)**  
  Outputs act like “messages” between jobs, ensuring later jobs know what earlier jobs produced.

- **[Real-world use](ca://s?q=Real_world_use_of_job_outputs)**  
  Commonly used for version tags, Docker image names, deployment URLs, or test results.

- **[Flexibility](ca://s?q=Flexibility_with_job_outputs)**  
  Makes workflows dynamic, because jobs can adapt based on values generated earlier.


![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-43/day43-task3.jpg)

[Click here to view workflow file](https://github.com/jeenicj97/github-actions-day43/blob/211785aa1a6e0b2047fcc37b0440ecc8f786f2d1/.github/workflows/job-outputs.yml)

---

### Task 4: Conditionals
In a workflow, add:
1. A step that only runs when the branch is `main`
2. A step that only runs when the previous step **failed**
3. A job that only runs on **push** events, not on pull requests
4. A step with `continue-on-error: true` — what does this do?

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-43/day43-task4.jpg)

[Click here to view workflow file](https://github.com/jeenicj97/github-actions-day43/blob/4f2f65d23f3cf96f70d8422d9d21288510acfdc8/.github/workflows/conditionals.yml)

---

### Task 5: Putting It Together
Create `.github/workflows/smart-pipeline.yml` that:
1. Triggers on push to any branch
2. Has a `lint` job and a `test` job running in parallel
3. Has a `summary` job that runs after both, prints whether it's a `main` branch push or a feature branch push, and prints the commit message

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-43/day43-task5.jpg)

[Click here to view workflow file](https://github.com/jeenicj97/github-actions-day43/blob/898d3d59cf3f56d322c62452d5643cdffe4e3039/.github/workflows/smart-pipeline.yml)

---

