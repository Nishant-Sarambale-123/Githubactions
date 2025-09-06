# Githubactions syntax

Got it 👍 You’re asking about **GitHub Actions syntax**. Let me break it down clearly with structure, keywords, and examples.

---

# 🔹 GitHub Actions Syntax

GitHub Actions workflows are defined in **YAML** files under:

```
.github/workflows/<workflow-name>.yml
```

---

## 1. **Basic Structure**

```yaml
name: CI Pipeline   # Name of your workflow (shows in GitHub UI)

on:                 # Triggers for workflow
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  workflow_dispatch: # Manual trigger

jobs:               # A workflow is made of one or more jobs
  build:
    runs-on: ubuntu-latest  # Runner (VM/OS to run job)
    
    steps:          # Each job has multiple steps
      - name: Checkout repository
        uses: actions/checkout@v3   # Reusable GitHub Action

      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm install

      - name: Run tests
        run: npm test
```

---

## 2. **Key Syntax Elements**

### 🔹 **Triggers (`on`)**

```yaml
on:
  push:                 # When code is pushed
    branches: [ main ]
  pull_request:         # On PRs
    branches: [ develop ]
  schedule:             # Cron jobs
    - cron: "0 0 * * *"
  workflow_dispatch:    # Manual trigger
```

---

### 🔹 **Jobs**

```yaml
jobs:
  job_name:
    runs-on: ubuntu-latest  # Runner type
    needs: other_job        # Dependency between jobs
    if: github.ref == 'refs/heads/main'  # Conditional execution
```

---

### 🔹 **Steps**

Each step runs either:

* a prebuilt **action** (`uses`)
* or a **script** (`run`)

```yaml
steps:
  - uses: actions/checkout@v3

  - name: Run shell command
    run: echo "Hello, World!"

  - name: Use a marketplace action
    uses: actions/upload-artifact@v3
    with:
      name: logs
      path: ./logs
```

---

### 🔹 **Environment Variables**

```yaml
env:
  NODE_ENV: production

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Env is $NODE_ENV"
```

---

### 🔹 **Matrix Strategy (parallel builds)**

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node: [14, 16, 18]
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

---

### 🔹 **Secrets & Variables**

```yaml
- name: Use secret
  run: echo "Secret is ${{ secrets.MY_SECRET }}"

- name: Use repo variable
  run: echo "Var is ${{ vars.ENVIRONMENT }}"
```

---

✅ That’s the core **syntax guide** for GitHub Actions.
I can also prepare a **ready-to-use template** for CI/CD (like Maven, Docker, Node.js, or Kubernetes) if you want.

Do you want me to make a **generic CI/CD GitHub Action template** for you (build → test → dockerize → deploy)?

Great question 👍 You’re asking about **Reusable Workflows** in GitHub Actions. These are powerful because they let you define a workflow once and then **call it from multiple other workflows**, avoiding duplication.

---

# 🔹 Reusable Workflows in GitHub Actions

## 1. **Where to Define a Reusable Workflow**

A reusable workflow must be in:

```
.github/workflows/<file>.yml
```

And it must be configured with:

```yaml
on:
  workflow_call:   # This makes the workflow reusable
```

---

## 2. **Example: Reusable Workflow**

**File: `.github/workflows/build.yml`**

```yaml
name: Reusable Build Workflow

on:
  workflow_call:
    inputs:                 # Optional input parameters
      node-version:
        required: true
        type: string
    secrets:                # Optional secret parameters
      NPM_TOKEN:
        required: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ inputs.node-version }}

      - run: npm install
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}

      - run: npm test
```

---

## 3. **Calling the Reusable Workflow**

**File: `.github/workflows/ci.yml`**

```yaml
name: Main CI

on:
  push:
    branches: [ main ]

jobs:
  call-build:
    uses: ./.github/workflows/build.yml    # Local reusable workflow
    with:
      node-version: '18'
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## 4. **Reusable Workflow from Another Repository**

You can even call a workflow defined in another repo:

```yaml
jobs:
  call-build:
    uses: org-name/repo-name/.github/workflows/build.yml@main
    with:
      node-version: '18'
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## 5. **Benefits**

✅ Avoid duplication across multiple workflows
✅ Keep CI/CD pipelines **modular & maintainable**
✅ Pass **inputs & secrets** dynamically
✅ Use across **repos** (centralized workflows for orgs)

---

👉 Now, to make it practical:
Do you want me to build you a **real-world example reusable workflow** for a CI/CD pipeline (like Maven build + Docker build + Deploy), so you can plug it into multiple projects?
