# Day 44 – Secrets, Artifacts & Running Real Tests in CI

---

## Challenge Tasks

### Task 1: GitHub Secrets
1. Go to your repo → Settings → Secrets and Variables → Actions
2. Create a secret called `MY_SECRET_MESSAGE`
3. Create a workflow that reads it and prints: `The secret is set: true` (never print the actual value)
4. Try to print `${{ secrets.MY_SECRET_MESSAGE }}` directly — what does GitHub show?

#### Why You Should Never Print Secrets in CI Logs
 
> Secrets should never be printed in CI logs because CI logs may be accessible to people who should not have access to the secret. Logs can also be retained, downloaded, copied, or exposed through debugging. GitHub masks many secret values automatically, but masking is a safety mechanism, not a replacement for keeping secrets out of logs.

### Task 2: Use Secrets as Environment Variables
1. Pass a secret to a step as an environment variable
2. Use it in a shell command without ever hardcoding it
3. Add `DOCKER_USERNAME` and `DOCKER_TOKEN` as secrets (you'll need these on Day 45)

[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day44/blob/main/.github/workflows/secrets.yml)

![Image Alt]()

---

### Task 3: Upload Artifacts
1. Create a step that generates a file — e.g., a test report or a log file
2. Use `actions/upload-artifact` to save it
3. After the workflow runs, download the artifact from the Actions tab

[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day44/blob/main/.github/workflows/artifacts.yml)

![Image Alt]()

---

### Task 4: Download Artifacts Between Jobs
1. Job 1: generate a file and upload it as an artifact
2. Job 2: download the artifact from Job 1 and use it (print its contents)

When are artifacts useful in real pipelines?
> Artifacts are useful when one job produces files that need to be preserved or consumed later. For example, a build job can create a Docker build context, test reports, application packages, or compiled binaries and upload them as artifacts. Another job can download and deploy or analyze those files.

[Click here to view the workflow](https://github.com/jeenicj97/github-actions-day44/blob/main/.github/workflows/artifact-between-jobs.yml)

![Image Alt]()

---

### Task 5: Run Real Tests in CI
Take any script from your earlier days (Python or Shell) and run it in CI:
1. Add your script to the `github-actions-practice` repo
2. Write a workflow that:
   - Checks out the code
   - Installs any dependencies needed
   - Runs the script
   - Fails the pipeline if the script exits with a non-zero code
3. Intentionally break the script — verify the pipeline goes red
4. Fix it — verify it goes green again

---

### Task 6: Caching
1. Add `actions/cache` to a workflow that installs dependencies
2. Run it twice — observe the time difference
3. Write in your notes: What is being cached and where is it stored?

---

## Hints
- Secrets: `${{ secrets.SECRET_NAME }}`
- Upload artifact: `uses: actions/upload-artifact@v4`
- Download artifact: `uses: actions/download-artifact@v4`
- Cache: `uses: actions/cache@v4`
- GitHub masks secret values in logs automatically

---

## Documentation
Create `day-44-secrets-artifacts.md` with:
- Screenshots of artifact download
- Screenshot of your passing test run
- What you learned about secrets management

---

