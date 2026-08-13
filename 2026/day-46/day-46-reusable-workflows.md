# Day 46 – Reusable Workflows & Composite Actions

---

## Challenge Tasks

### Task 1: Understand `workflow_call`
Before writing any code, research and answer in your notes:
1. What is a **reusable workflow**?
   
A **reusable workflow** is a GitHub Actions workflow that can be called by another workflow. Instead of writing the same jobs repeatedly across repositories, you create one workflow and reuse it wherever needed.
   
Benefits:
- [Reduces duplicate code](ca://s?q=Explain_how_reusable_workflows_reduce_duplicate_code)  
- [Easier maintenance](ca://s?q=Explain_easier_maintenance_with_reusable_workflows)  
- [Standardizes CI/CD](ca://s?q=Explain_standardization_in_CI_CD_with_reusable_workflows) across repositories  
- [Can accept inputs, secrets, and outputs](ca://s?q=Explain_inputs_secrets_outputs_in_reusable_workflows)
  
2. What is the `workflow_call` trigger?  

`workflow_call` is a special trigger that allows a workflow to be invoked by another workflow

Example
```yaml
on:
  workflow_call:
```

3. How is calling a reusable workflow different from using a regular action (`uses:`)?  

Reusable Workflow vs Regular Action
| **[Reusable Workflow](ca://s?q=Explain_reusable_workflow_in_GitHub_Actions)** | **[Regular Action](ca://s?q=Explain_regular_action_in_GitHub_Actions)** |
|---------------------------------------------|-----------------------------------------|
| [Can contain multiple jobs](ca://s?q=Reusable_workflow_multiple_jobs) | [Usually contains steps only](ca://s?q=Regular_action_contains_steps_only) |
| [Has its own runners](ca://s?q=Reusable_workflow_own_runners) | [Runs inside the caller job](ca://s?q=Regular_action_runs_inside_caller_job) |
| [Uses workflow_call](ca://s?q=workflow_call_in_reusable_workflow) | [Uses action.yml](ca://s?q=action_yml_in_regular_action) |
| [Can define inputs, secrets, outputs](ca://s?q=Reusable_workflow_inputs_secrets_outputs) | [Can define inputs and outputs](ca://s?q=Regular_action_inputs_outputs) |
| [Best for complete CI/CD pipelines](ca://s?q=Reusable_workflow_for_CI_CD_pipelines) | [Best for reusable step logic](ca://s?q=Regular_action_for_step_logic) |

4. Where must a reusable workflow file live?

Reusable workflows must be stored inside the **[.github/workflows](ca://s?q=Explain_.github_workflows_directory_in_GitHub_Actions)** directory.

Example:

```plaintext
.github/workflows/reusable-build.yml
```


---

### Task 2: Create Your First Reusable Workflow
Create `.github/workflows/reusable-build.yml`:
1. Set the trigger to `workflow_call`
2. Add an `inputs:` section with:
   - `app_name` (string, required)
   - `environment` (string, required, default: `staging`)
3. Add a `secrets:` section with:
   - `docker_token` (required)
4. Create a job that:
   - Checks out the code
   - Prints `Building <app_name> for <environment>`
   - Prints `Docker token is set: true` (never print the actual secret)

**Verify:** This file alone won't run — it needs a caller. That's next.

---

### Task 3: Create a Caller Workflow
Create `.github/workflows/call-build.yml`:
1. Trigger on push to `main`
2. Add a job that uses your reusable workflow:
   ```yaml
   jobs:
     build:
       uses: ./.github/workflows/reusable-build.yml
       with:
         app_name: "my-web-app"
         environment: "production"
       secrets:
         docker_token: ${{ secrets.DOCKER_TOKEN }}
   ```
3. Push to `main` and watch it run

**Verify:** In the Actions tab, do you see the caller triggering the reusable workflow? Click into the job — can you see the inputs printed?

---

### Task 4: Add Outputs to the Reusable Workflow
Extend `reusable-build.yml`:
1. Add an `outputs:` section that exposes a `build_version` value
2. Inside the job, generate a version string (e.g., `v1.0-<short-sha>`) and set it as output
3. In your caller workflow, add a second job that:
   - Depends on the build job (`needs:`)
   - Reads and prints the `build_version` output

**Verify:** Does the second job print the version from the reusable workflow?

---

### Task 5: Create a Composite Action
Create a **custom composite action** in your repo at `.github/actions/setup-and-greet/action.yml`:
1. Define inputs: `name` and `language` (default: `en`)
2. Add steps that:
   - Print a greeting in the specified language
   - Print the current date and runner OS
   - Set an output called `greeted` with value `true`
3. Use the composite action in a new workflow with `uses: ./.github/actions/setup-and-greet`

**Verify:** Does your custom action run and print the greeting?

---

### Task 6: Reusable Workflow vs Composite Action
Fill this in your notes:

| | Reusable Workflow | Composite Action |
|---|---|---|
| Triggered by | `workflow_call` | `uses:` in a step |
| Can contain jobs? | ? | ? |
| Can contain multiple steps? | ? | ? |
| Lives where? | ? | ? |
| Can accept secrets directly? | ? | ? |
| Best for | ? | ? |

---

## Hints
- Reusable workflows must be in `.github/workflows/` directory
- Caller syntax: `uses: ./.github/workflows/file.yml` (same repo) or `uses: org/repo/.github/workflows/file.yml@main` (cross-repo)
- Composite action: `action.yml` with `runs: using: "composite"`
- Reusable workflow outputs: `on: workflow_call: outputs: name: value: ${{ jobs.job-id.outputs.name }}`
- A reusable workflow can be called by at most 20 unique caller workflows in a single run

---

## Documentation
Create `day-46-reusable-workflows.md` with:
- Your reusable workflow and caller workflow YAML
- Your composite action YAML
- The comparison table from Task 6
- Screenshot of the caller workflow triggering the reusable one

---
