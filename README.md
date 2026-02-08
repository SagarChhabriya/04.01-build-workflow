# 04.01-build-workflow





## 1. What a “build workflow” actually is

A **build workflow** is an automated checklist that runs when something happens, like:

* you push code
* you open a pull request
* you create a release

Typical goals:

* set up Python
* install dependencies
* run tests
* optionally package the project

Think of it as:

> “Every time code changes, do these steps exactly the same way, automatically.”

---

## 2. Where build workflows live (GitHub Actions)

In your repository:

```
.github/
  workflows/
    build.yml
```

That `.yml` file **is** the workflow.

---

## 3. The basic structure (big picture)

A workflow has 4 core ideas:

1. **When** it runs → `on`
2. **What machine** it runs on → `runs-on`
3. **Jobs** → groups of work
4. **Steps** → individual commands

Visually:

```
Workflow
 └── Job
      └── Steps
           ├── checkout code
           ├── set up Python
           ├── install dependencies
           └── run tests
```

---

## 4. A minimal real example (Python)

Here’s a **simple Python build workflow** that runs on every push to `main`:

```yaml
name: Build

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: pytest
```

Now let’s break it down.

---

## 5. Line-by-line explanation (the important bits)

### `name`

```yaml
name: Build
```

The label you see in the GitHub Actions UI.

---

### `on`

```yaml
on:
  push:
    branches: [ "main" ]
```

This workflow runs whenever someone pushes to the `main` branch.

---

### `jobs`

```yaml
jobs:
  build:
```

Defines a job called `build`.

You can have multiple jobs, such as `lint`, `test`, or `deploy`.

---

### `runs-on`

```yaml
runs-on: ubuntu-latest
```

GitHub provides a fresh Linux virtual machine to run your workflow.

---

### `steps`

Each step is either:

* an **action** (`uses:`)
* a **shell command** (`run:`)

Example:

```yaml
- uses: actions/checkout@v4
```

This pulls your repository code into the runner.

```yaml
- run: pip install -r requirements.txt
```

This runs a terminal command on the runner.

---

## 6. What “build” means for Python

In Python, “build” usually means one or more of the following:

| Project type     | Build step                   |
| ---------------- | ---------------------------- |
| Script / library | Install dependencies         |
| Web app          | Install + run tests          |
| Package          | Build wheel or source dist   |
| Data project     | Validate environment + tests |

The workflow just runs commands. What those commands are depends on your project.

---

## 7. Common beginner upgrades

Once the basics work, people usually add:

### Run on pull requests

```yaml
on:
  push:
  pull_request:
```

---

### Cache dependencies (faster builds)

```yaml
- uses: actions/setup-python@v5
  with:
    python-version: "3.11"
    cache: "pip"
```

---

### Separate jobs

```yaml
jobs:
  lint:
  test:
```

This allows jobs to run in parallel or have different responsibilities.

---

## 8. Mental model to remember

A build workflow is:

> “On event X, spin up a clean machine, set up Python, run these commands, and fail if anything breaks.”

Nothing more than automation plus consistency.


---

# Example: Level = EASY

### Goal

Confirm that:

* GitHub Actions runs
* Python installs
* pytest executes

This is the **minimum possible CI check**.

---

## Instructions

1. Create a new GitHub repo
2. Add the files exactly as shown
3. Push to `main`
4. Check **Actions → Build**

---

## Repo structure

```
.
├── test_basic.py
├── requirements.txt
└── .github/
    └── workflows/
        └── build.yml
```

---

## Code

### `requirements.txt`

```txt
pytest
```

### `test_basic.py`

```python
def test_basic_math():
    assert 1 + 1 == 2
```

---

## Workflow: `.github/workflows/build.yml`

```yaml
name: Build

on:
  push:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: pytest
```

---

## What this proves

* GitHub Actions works
* Python is configured
* Tests run successfully
