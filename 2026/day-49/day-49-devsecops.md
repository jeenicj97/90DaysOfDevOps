# Day 49 – DevSecOps: Add Security to Your CI/CD Pipeline

---

## What is DevSecOps?

Think of it like this:

**Without DevSecOps:**
> You build the app → deploy it → a security team finds a vulnerability weeks later → you scramble to fix it

**With DevSecOps:**
> You open a PR → the pipeline automatically checks for vulnerabilities → you fix it before it ever gets merged

**That's it.** DevSecOps = adding security checks to the pipeline you already have. Not a separate process — just a few extra steps.

---

## Key Principles (Keep These in Mind)

1. **Catch problems early** — A vulnerability found in a PR takes 5 minutes to fix. The same vulnerability found in production takes days.

2. **Automate the checks** — Don't rely on someone remembering to check. Let the pipeline do it every time.

3. **Block on critical issues** — If a scan finds a serious vulnerability, the pipeline should fail — just like a failing test.

4. **Never put secrets in code** — Use GitHub Secrets (you learned this on Day 44). No `.env` files, no hardcoded API keys.

5. **Give only the access needed** — Your workflow doesn't need write access to everything. Limit permissions.

---

## Challenge Tasks

### Task 1: Scan Your Docker Image for Vulnerabilities
Your Docker image might use a base image with known security issues. Let's find out.

Add this step to your main branch pipeline (after Docker build, before deploy):
```yaml
- name: Scan Docker Image for Vulnerabilities
  uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'your-username/your-app:latest'
    format: 'table'
    exit-code: '1'
    severity: 'CRITICAL,HIGH'
```
What this does:
- `trivy` scans your Docker image for known CVEs (Common Vulnerabilities and Exposures)
- `format: 'table'` prints a readable table in the logs
- `exit-code: '1'` means **fail the pipeline** if CRITICAL or HIGH vulnerabilities are found
- If it passes, your image is clean — proceed to push and deploy

Push and check the Actions tab. Read the scan output.

**Verify:** Can you see the vulnerability table in the logs? Did it pass or fail?

Write in your notes: What CVEs (if any) were found? What base image are you using?  

  * Found below CVE's. To correct this - I replaced slim to alpine image size.
    
    ```
    jeenicj97/gha-capstone:latest (debian 13.6)
      ===========================================
      Total: 4 (CRITICAL: 4)
      
      ┌───────────┬────────────────┬──────────┬──────────────┬───────────────────┬───────────────┬──────────────────────────────────────────────────────────────┐
      │  Library  │ Vulnerability  │ Severity │    Status    │ Installed Version │ Fixed Version │                            Title                             │
      ├───────────┼────────────────┼──────────┼──────────────┼───────────────────┼───────────────┼──────────────────────────────────────────────────────────────┤
      │ perl-base │ CVE-2026-13221 │ CRITICAL │ affected     │ 5.40.1-6          │               │ Perl versions through 5.43.9 produce silently incorrect      │
      │           │                │          │              │                   │               │ regular expres ...                                           │
      │           │                │          │              │                   │               │ https://avd.aquasec.com/nvd/cve-2026-13221                   │
      │           ├────────────────┤          ├──────────────┤                   ├───────────────┼──────────────────────────────────────────────────────────────┤
      │           │ CVE-2026-42496 │          │ fix_deferred │                   │               │ perl-archive-tar: perl-archive-tar: Path traversal via       │
      │           │                │          │              │                   │               │ crafted symlinks allows arbitrary file access                │
      │           │                │          │              │                   │               │ https://avd.aquasec.com/nvd/cve-2026-42496                   │
      │           ├────────────────┤          ├──────────────┤                   ├───────────────┼──────────────────────────────────────────────────────────────┤
      │           │ CVE-2026-57433 │          │ affected     │                   │               │ Storable versions before 3.41 for Perl have a signed integer │
      │           │                │          │              │                   │               │ overflow ...                                                 │
      │           │                │          │              │                   │               │ https://avd.aquasec.com/nvd/cve-2026-57433                   │
      │           ├────────────────┤          │              │                   ├───────────────┼──────────────────────────────────────────────────────────────┤
      │           │ CVE-2026-8376  │          │              │                   │               │ perl: Perl: Heap buffer overflow when compiling regular      │
      │           │                │          │              │                   │               │ expressions on 32-bit builds...                              │
      │           │                │          │              │                   │               │ https://avd.aquasec.com/nvd/cve-2026-8376                    │
      └───────────┴────────────────┴──────────┴──────────────┴───────────────────┴───────────────┴──────────────────────────────────────────────────────────────┘
      Error: Process completed with exit code 1.
    ```


![Image Alt]()


---

### Task 2: Enable GitHub's Built-in Secret Scanning
GitHub can automatically detect if someone pushes a secret (API key, token, password) to your repo.

1. Go to your repo → Settings → **Code security and analysis**
2. Enable **Secret scanning**
3. If available, also enable **Push protection** — this blocks the push entirely if a secret is detected

That's it — no workflow changes needed. GitHub does this automatically.

![Image Alt]()

![Image Alt]()


Write in your notes:
- What is the difference between secret scanning and push protection?

| Feature | Secret Scanning | Push Protection |
|---------|-----------------|-----------------|
| Timing  | Runs **after push** | Runs **during push** |
| Action  | Finds secrets already committed | Stops the push before it reaches GitHub |
| Purpose | Detects exposed credentials | Prevents exposure |
| Example | If GitHub detects an AWS Key → Creates a **security alert** | If GitHub detects an AWS Key → **Blocks the push** (if enabled) |

- What happens if GitHub detects a leaked AWS key in your repo?

    - **Push Protection**: Blocks the push before secrets reach GitHub.  
    - **Secret Scanning**: Raises a security alert if secrets are already committed.  
    - **Best Practice**: Immediately **revoke and rotate** the AWS key.

---

### Task 3: Scan Dependencies for Known Vulnerabilities
If your app uses packages (pip, npm, etc.), those packages might have known vulnerabilities.

Add this to your **PR pipeline** (not the main pipeline):
```yaml
- name: Check Dependencies for Vulnerabilities
  uses: actions/dependency-review-action@v4
  with:
    fail-on-severity: critical
```

This checks any **new** dependencies added in the PR against a vulnerability database. If a dependency has a critical CVE, the PR check fails.

Test it:
1. Open a PR that adds a package to your app
2. Check the Actions tab — did the dependency review run?

**Verify:** Does the dependency review show up as a check on your PR?

![Image Alt]()

---

### Task 4: Add Permissions to Your Workflows
By default, workflows get broad permissions. Lock them down.

Add this block near the top of your workflow files (after `on:`):
```yaml
permissions:
  contents: read
```

If a workflow needs to comment on PRs, add:
```yaml
permissions:
  contents: read
  pull-requests: write
```

Update at least 2 of your existing workflow files with a `permissions` block.

Write in your notes: Why is it a good practice to limit workflow permissions? What could go wrong if a compromised action has write access to your repo?

  #### Why is it good practice to limit workflow permissions?
  - **Principle of Least Privilege**: Workflows should only have the access they truly need.
  - **Reduced Attack Surface**: Minimizes the risk if an action or dependency is compromised.
  - **Repository Integrity**: Prevents unauthorized or accidental modifications to code, issues, or pull requests.
  - **Security Compliance**: Aligns with best practices for secure CI/CD pipelines.
  
  #### What could go wrong with broad permissions?
  - **Push malicious code** into your repository.
  - **Modify or delete files**, breaking builds or pipelines.
  - **Tamper with pull requests** (e.g., approving or merging without review).
  - **Exfiltrate sensitive data** such as secrets or tokens.
  
  **Bottom line**: Restricting permissions ensures workflows remain safe, predictable, and trustworthy.



---

### Task 5: See the Full Secure Pipeline
Look at what your pipeline does now:

```
PR opened
  → build & test
  → dependency vulnerability check     ← NEW (Day 49)
  → PR checks pass or fail

Merge to main
  → build & test
  → Docker build
  → Trivy image scan (fail on CRITICAL) ← NEW (Day 49)
  → Docker push (only if scan passes)
  → deploy

Always active
  → GitHub secret scanning              ← NEW (Day 49)
  → push protection for secrets         ← NEW (Day 49)
```

Draw this diagram in your notes. You just built a **DevSecOps pipeline** — security is now part of your automation, not an afterthought.

```
                Pull Request
                      │
                      ▼
              Build & Test
                      │
                      ▼
          Dependency Review Scan
                      │
                      ▼
            PR Checks Pass/Fail

──────────────────────────────────────

               Merge to Main
                      │
                      ▼
              Build & Test
                      │
                      ▼
             Docker Build
                      │
                      ▼
         Trivy Image Vulnerability Scan
                      │
          (Fail on HIGH/CRITICAL)
                      │
                      ▼
             Docker Push
                      │
                      ▼
          Deploy to Production

──────────────────────────────────────

        GitHub Secret Scanning
        Push Protection
        (Always Active)
```

---

## Brownie Points (Optional — For the Curious)

### Pin Actions to Commit SHAs
Tags like `@v4` can be moved by the action author. For extra security, pin to the exact commit:
```yaml
# Instead of this:
uses: actions/checkout@v4

# Use this:
uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
```
This protects against supply chain attacks where a tag is silently changed.

### Upload Scan Results to GitHub Security Tab
Add SARIF output to Trivy and upload it — your scan results will appear in the repo's **Security** tab:
```yaml
- uses: aquasecurity/trivy-action@master
  with:
    image-ref: 'your-username/your-app:latest'
    format: 'sarif'
    output: 'trivy-results.sarif'
- uses: github/codeql-action/upload-sarif@v3
  with:
    sarif_file: 'trivy-results.sarif'
```

![Image Alt]()


### Learn About OIDC (Keyless Authentication)  

  Instead of storing long-lived cloud credentials in GitHub Secrets, GitHub Actions can use OpenID Connect (OIDC) to obtain temporary, short-lived access tokens directly from cloud providers such as AWS, Azure, or GCP.      This improves security by eliminating the need to store permanent credentials in the repository.

---

## Hints
- Trivy action docs: look up `aquasecurity/trivy-action` on GitHub
- `exit-code: '1'` = fail the step, `exit-code: '0'` = just warn
- Dependency review only works on `pull_request` events (not on push)
- Permissions block goes at the workflow level or the job level
- GitHub secret scanning is free for public repos

---

## Documentation
- What DevSecOps means in your own words (2-3 sentences)
  
    DevSecOps integrates security into the CI/CD pipeline so vulnerabilities are detected automatically during development instead of after deployment. By automating security checks, teams can identify and fix issues earlier, reducing the risk of deploying vulnerable applications.

- What you learned about secret scanning and dependency review

  #### Secret Scanning  
  
  Secret Scanning detects secrets that have already been committed to the repository.  
  Push Protection blocks commits containing supported secrets before they are pushed to GitHub.  
  If GitHub detects a leaked AWS access key, it generates a security alert (or blocks the push if Push Protection is enabled). The exposed credential should be revoked and replaced immediately.
  
  #### Dependency Review  
  
  The Dependency Review Action checks newly introduced dependencies in pull requests against GitHub's vulnerability database. If a dependency contains a critical vulnerability, the PR check fails, preventing the insecure dependency from being merged.

---

#### What I Learned

- Security can be integrated directly into CI/CD pipelines.
- Trivy scans Docker images for known vulnerabilities (CVEs).
- Dependency Review helps detect vulnerable packages before merging.
- Secret Scanning and Push Protection help prevent credential leaks.
- Limiting workflow permissions follows the principle of least privilege and reduces security risks.
  
---
