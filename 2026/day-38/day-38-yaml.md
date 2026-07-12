# Day 38 – YAML Basics

---

## Challenge Tasks

### Task 1: Key-Value Pairs
Create `person.yaml` that describes yourself with:
- `name`
- `role`
- `experience_years`
- `learning` (a boolean)

#### person.yaml
```yaml
name: Jeeni
role: DevOps Engineer
experience_years: 8
learning: true
```

---

### Task 2: Lists
Add to `person.yaml`:
- `tools` — a list of 5 DevOps tools you know or are learning
- `hobbies` — a list using the inline format `[item1, item2]`

Write in your notes: What are the two ways to write a list in YAML?

```yaml
#Way1
tools:
  - Jenkins
  - GitHub Actions
  - Terraform
  - Kubernetes
  - Shell Scripting

#Way2
hobbies: [reading, cooking, listening to music]
```

---

### Task 3: Nested Objects
Create `server.yaml` that describes a server:
- `server` with nested keys: `name`, `ip`, `port`
- `database` with nested keys: `host`, `name`, `credentials` (nested further: `user`, `password`)

#### Structure

```
server
 ├── name
 ├── ip
 └── port

database
 ├── host
 ├── name
 └── credentials
      ├── user
      └── password
```

#### server.yaml

```yaml
server:
  name: app-server
  ip: 10.25.270.0
  port: 8080

database:
  host: postgres-db
  name: employee_db

  credentials:
    user: postgres-user
    password: password
      
```

---

### Task 4: Multi-line Strings
In `server.yaml`, add a `startup_script` field using:
1. The `|` block style (preserves newlines)
2. The `>` fold style (folds into one line)

Write in your notes: When would you use `|` vs `>`?
 * Use | when you want to preserve formatting (scripts, configs).
 * Use > when you want to fold text into one line (messages, paragraphs).
   
#### Pipe Style ( | )

Preserves line breaks.

```yaml
startup_script: |
  echo "Starting Application"
  mkdir logs
  python app.py
```

Equivalent to:
```
echo "Starting Application"
mkdir logs
python app.py
```
Each line remains separate.

#### Folded Style ( > )

```yaml
startup_message: >
  Application
  Started
  Successfully
```
Equivalent to:
Application Started Successfully

Newlines become spaces.

#### server.yaml
```yaml

server:
  name: app-server
  ip: 192.168.1.10
  port: 8080

database:
  host: postgres-db
  name: employee_db

  credentials:
    user: postgres
    password: password

startup_script: |
  echo "Starting Application"
  mkdir logs
  python app.py

startup_message: >
  Application
  Started
  Successfully

```
---

### Task 5: Validate Your YAML
1. Install `yamllint` or use an online validator
   
   * sudo apt install yamllint
     
3. Validate both your YAML files

   ```yaml
   jeenicj@DESKTOP-BG3MAVI:~/yaml-prac$ yamllint person.yml
   jeenicj@DESKTOP-BG3MAVI:~/yaml-prac$ yamllint server.yml
   ```
4. Intentionally break the indentation — what error do you get?

   ```yaml
   jeenicj@DESKTOP-BG3MAVI:~/yaml-prac$ yamllint server.yml
    server.yml
    2:3       error    wrong indentation: expected 0 but found 2  (indentation)
    7:1       error    syntax error: expected '<document start>', but found '<block mapping start>' (syntax)
   ```
6. Fix it and validate again
   ```yaml
   jeenicj@DESKTOP-BG3MAVI:~/yaml-prac$ yamllint server.yml
   jeenicj@DESKTOP-BG3MAVI:~/yaml-prac$
   ```

---

### Task 6: Spot the Difference
Read both blocks and write what's wrong with the second one:

```yaml
# Block 1 - correct
name: devops
tools:
  - docker
  - kubernetes
```

```yaml
# Block 2 - broken
name: devops
tools:
- docker
  - kubernetes
```

* The indentation is inconsistent
* The second list item is indented differently
* YAML expects all items in the same list to align


### What you learnt (3 points)
---
