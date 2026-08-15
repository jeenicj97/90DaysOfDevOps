# Day 47 – Advanced Triggers: PR Events, Cron Schedules & Event-Driven Pipelines

---

## Challenge Tasks

### Task 1: Pull Request Event Types
Create `.github/workflows/pr-lifecycle.yml` that triggers on `pull_request` with **specific activity types**:
1. Trigger on: `opened`, `synchronize`, `reopened`, `closed`
2. Add steps that:
   - Print which event type fired: `${{ github.event.action }}`
   - Print the PR title: `${{ github.event.pull_request.title }}`
   - Print the PR author: `${{ github.event.pull_request.user.login }}`
   - Print the source branch and target branch
3. Add a conditional step that only runs when the PR is **merged** (closed + merged = true)

[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day47/blob/7ef690f2a8f26a38eabfe5206b228e43582bdbea/.github/workflows/pr-lifecycle.yml)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task1.jpg)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task1pr-merged.jpg)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task1pr-not.jpg)


---

### Task 2: PR Validation Workflow
Create `.github/workflows/pr-checks.yml` — a real-world PR gate:
1. Trigger on `pull_request` to `main`
2. Add a job `file-size-check` that:
   - Checks out the code
   - Fails if any file in the PR is larger than 1 MB
3. Add a job `branch-name-check` that:
   - Reads the branch name from `${{ github.head_ref }}`
   - Fails if it doesn't follow the pattern `feature/*`, `fix/*`, or `docs/*`
4. Add a job `pr-body-check` that:
   - Reads the PR body: `${{ github.event.pull_request.body }}`
   - Warns (but doesn't fail) if the PR description is empty

[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day47/blob/1580809cd943e2eb1a3ca95c58dcf0c9c8e6ac6a/.github/workflows/pr-checks.yml)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task2.jpg)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task2.2.jpg)


---

### Task 3: Scheduled Workflows (Cron Deep Dive)
Create `.github/workflows/scheduled-tasks.yml`:
1. Add a `schedule` trigger with cron: `'30 2 * * 1'` (every Monday at 2:30 AM UTC)
2. Add **another** cron entry: `'0 */6 * * *'` (every 6 hours)
3. In the job, print which schedule triggered using `${{ github.event.schedule }}`
4. Add a step that acts as a **health check** — curl a URL and check the response code

Write in your notes:
- The cron expression for: every weekday at 9 AM IST
  GitHub uses UTC
  9:00 AM IST = 3:30 AM UTC  
  `30 3 * * 1-5`
  
- The cron expression for: first day of every month at midnight  
  `0 0 1 * *`
  
- Why GitHub says scheduled workflows may be delayed or skipped on inactive repos  
   * GitHub Actions shares runners among repositories.
   * Scheduled jobs are best effort, not guaranteed exactly on time.
   * Inactive repositories may have scheduled workflows disabled or delayed.
   * High platform load can also delay execution.



[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day47/blob/main/.github/workflows/scheduled-tasks.yml)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task3.jpg)

---

### Task 4: Path & Branch Filters
Create `.github/workflows/smart-triggers.yml`:
1. Trigger on push but **only** when files in `src/` or `app/` change:
   ```yaml
   on:
     push:
       paths:
         - 'src/**'
         - 'app/**'
   ```
2. Add `paths-ignore` in a second workflow that skips runs when only docs change:
   ```yaml
   paths-ignore:
     - '*.md'
     - 'docs/**'
   ```
3. Add branch filters to only trigger on `main` and `release/*` branches
4. Test it: push a change to a `.md` file — does the workflow skip?

Write in your notes: When would you use `paths` vs `paths-ignore`?

[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day47/blob/main/.github/workflows/smart-triggers.yml)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task4.1.jpg)


---

### Task 5: `workflow_run` — Chain Workflows Together
Create two workflows:
1. `.github/workflows/tests.yml` — runs tests on every push
2. `.github/workflows/deploy-after-tests.yml` — triggers **only after** `tests.yml` completes successfully:
   ```yaml
   on:
     workflow_run:
       workflows: ["Run Tests"]
       types: [completed]
   ```
3. In the deploy workflow, add a conditional:
   - Only proceed if the triggering workflow **succeeded** (`${{ github.event.workflow_run.conclusion == 'success' }}`)
   - Print a warning and exit if it failed


[Click here to view test workflow](https://github.com/jeenicj97/github-actions-day47/blob/main/.github/workflows/tests.yml)

[Click here to view deploy-after-test workflow](https://github.com/jeenicj97/github-actions-day47/blob/main/.github/workflows/deploy-after-tests.yml)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task5.jpg)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/master/2026/day-47/day47-task5.1.jpg)

---

### Task 6: `repository_dispatch` — External Event Triggers
1. Create `.github/workflows/external-trigger.yml` with trigger `repository_dispatch`
2. Set it to respond to event type: `deploy-request`
3. Print the client payload: `${{ github.event.client_payload.environment }}`
4. Trigger it using `curl` or `gh`:
   ```bash
   gh api repos/<owner>/<repo>/dispatches \
     -f event_type=deploy-request \
     -f client_payload='{"environment":"production"}'
   ```

Write in your notes: When would an external system (like a Slack bot or monitoring tool) trigger a pipeline?  

An external system uses repository_dispatch when the trigger condition isn't something GitHub can see on its own — no commit, PR, or schedule involved. A few real examples:  

   * ChatOps — someone types /deploy production in Slack, and a bot calls the dispatch API to kick off the deploy workflow.
   * Monitoring tools — Datadog/PagerDuty detects an error spike and auto-triggers a rollback workflow.
   * Cross-repo pipelines — a shared library repo publishes a new version and dispatches a rebuild trigger to a dependent app repo.
   * CMS webhooks — someone publishes content in a headless CMS, triggering a site rebuild with no code change involved.  

The common thread: the event happened outside your repo, so GitHub needs to be told about it via API instead of observing it directly.


[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day47/blob/main/.github/workflows/external-trigger.yml)

---

