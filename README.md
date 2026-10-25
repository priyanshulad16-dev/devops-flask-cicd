# Automated CI/CD Pipeline for Containerized Flask Application

A lightweight, automated Continuous Integration and Continuous Delivery (CI/CD) pipeline built using **GitHub Actions**, **Docker**, and **Python Flask**. 

This repository demonstrates automated code quality validation, unit testing, and container image generation on every push and pull request to the `main` branch.

---

## Architecture & Workflow Overview

```text
[ Developer Push / PR ] 
         │
         ▼
[ GitHub Actions Runner ]
         │
         ├── 1. Setup Python Environment
         ├── 2. Install Dependencies (pip)
         ├── 3. Code Linting (Flake8)
         ├── 4. Automated Unit Testing (Pytest)
         │
         ▼ (Only if tests pass)
         └── 5. Build Docker Image (Docker Buildx)
```

---

## Tech Stack

* **Application Framework:** Python 3, Flask
* **Testing & Linting:** Pytest, Flake8
* **Containerization:** Docker (`python:3.11-slim`)
* **CI/CD Automation:** GitHub Actions
* **Version Control:** Git, GitHub

---

## Project Structure

```text
devops-flask-cicd/
├── .github/
│   └── workflows/
│       └── cicd.yml          # GitHub Actions pipeline workflow definition
├── tests/
│   └── test_app.py           # Automated unit tests using pytest
├── .dockerignore              # Excludes virtual environments and build cache
├── .gitignore                # Excludes pycache and virtual environments from Git
├── Dockerfile                # Optimized multi-stage/slim container build recipe
├── app.py                    # Flask application entry point with health checks
└── requirements.txt          # Python application and test dependencies
```

---

## API Endpoints

| Method | Endpoint  | Description | Expected Response |
| :---   | :---      | :---        | :---              |
| `GET`  | `/`       | Root status check | `{"message": "CI/CD Pipeline is working!"}` |
| `GET`  | `/health` | Application health probe | `{"status": "healthy"}` |

---

## Local Setup & Development

### 1. Clone the Repository
```bash
git clone [https://github.com/priyanshulad16-dev/devops-flask-cicd.git](https://github.com/priyanshulad16-dev/devops-flask-cicd.git)
cd devops-flask-cicd
```

### 2. Set Up Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 3. Run Linting and Unit Tests
```bash
# Run lint check
flake8 . --count --select=E9,F63,F7,F82 --show-source --statistics

# Run test suite
python3 -m pytest
```

### 4. Build and Run Container Locally
```bash
# Build the Docker image
docker build -t flask-cicd-app:v1 .

# Run the container
docker run -d -p 5000:5000 --name flask-app flask-cicd-app:v1

# Verify the application
curl http://localhost:5000/
```

---

## CI/CD Pipeline Stages

The GitHub Actions workflow defined in `.github/workflows/cicd.yml` runs on every push and pull request to `main`:

1. **`ci-test` Job:**
   * Checks out the latest code.
   * Sets up Python 3.11 with cached pip dependencies.
   * Runs `flake8` to enforce PEP 8 standards and catch syntax defects.
   * Executes `pytest` against `/` and `/health` endpoints.

2. **`docker-build` Job:**
   * Depends directly on `ci-test` succeeding (`needs: ci-test`).
   * Configures Docker Buildx.
   * Builds the Docker image tagged with the commit SHA (`${{ github.sha }}`) to guarantee reproducible builds.
