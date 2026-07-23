# Day 40 – Your First GitHub Actions Workflow

---

## Challenge Tasks

### Task : Hello Workflow
Create `.github/workflows/hello.yml` with a workflow that:
1. Triggers on every `push`
2. Has one job called `greet`
3. Runs on `ubuntu-latest`
4. Has two steps:
   - Step 1: Check out the code using `actions/checkout`
   - Step 2: Print `Hello from GitHub Actions!`

```yaml


name: First workflow

on: push

jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Print Hello
        run: echo "Hello from GHA"

```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/264027e86112923d8e1fad8dd01a04129845567f/2026/day-40/day40-1.jpg)

---

### Task : Understand the Anatomy
Look at your workflow file and write in your notes what each key does:
- `on:` defines the event that triggers the workflow (push, pull_request, workflow dispatch)
- `jobs:` defines one or more jobs that the workflow will execute (can have multiple jobs like build, test, deploy)
- `runs-on:` specifies the runner OS (ubuntu-latest, windows-latest, macos-latest)
- `steps:` a job consists of multiple steps (each step performs one action)
- `uses:` uses an existing GitHub Action (it downloads your repository onto your runner - actions/checkout@v4) 
- `run:` runs shell command (echo "hello")
- `name:` (on a step) - provides a readable name

---

### Task : Add More Steps
Update `hello.yml` to also:
1. Print the current date and time
2. Print the name of the branch that triggered the run (hint: GitHub provides this as a variable)
3. List the files in the repo
4. Print the runner's operating system

```yaml


name: First workflow

on: push

jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      - name: Print Hello
        run: echo "Hello from GitHub Actions"
      - name: Print current date & time
        run: date
      - name: Print branch name
        run: echo "Branch is ${{ github.ref_name }}"
      - name: List files in repo
        run: ls -la
      - name: Print runner OS
        run: echo "Running on $RUNNER_OS"

```

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/264027e86112923d8e1fad8dd01a04129845567f/2026/day-40/da-40.2.jpg)

---

### Task 5: Break It On Purpose
1. Add a step that runs a command that will **fail** (e.g., `exit 1` or a misspelled command)
2. Push and observe what happens in the Actions tab
3. Fix it and push again

```
The workflow run is marked with a red X.
The failed job is highlighted.
The failed step shows an error message.
Logs indicate which command failed and its exit code.

To troubleshoot:

Open the failed workflow run.
Click the failed job.
Expand the failed step.
Read the command output and exit code.
Correct the issue and push the changes
```

---
