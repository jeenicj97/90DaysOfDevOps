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

![Image Alt]()

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

### Task 4: Add More Steps
Update `hello.yml` to also:
1. Print the current date and time
2. Print the name of the branch that triggered the run (hint: GitHub provides this as a variable)
3. List the files in the repo
4. Print the runner's operating system

Push again — watch the new run.

---

### Task 5: Break It On Purpose
1. Add a step that runs a command that will **fail** (e.g., `exit 1` or a misspelled command)
2. Push and observe what happens in the Actions tab
3. Fix it and push again

Write in your notes: What does a failed pipeline look like? How do you read the error?

---

## Hints
- Workflow files live in `.github/workflows/` and must end in `.yml`
- `uses: actions/checkout@v4` checks out your code onto the runner
- `run:` executes shell commands
- GitHub provides built-in variables like `${{ github.ref_name }}` for branch name
- Every push triggers a new run — check the Actions tab

---
