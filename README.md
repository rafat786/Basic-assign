# End-to-End CI/CD Pipeline using GitHub Actions

## Objective
This project demonstrates an automated CI/CD workflow for a small Python Flask application. GitHub Actions builds the application environment, runs unit tests, builds a Docker image, and publishes the image to Docker Hub.

## Technology stack
- Git and GitHub
- GitHub Actions
- Python 3.11 and Flask
- Pytest
- Docker and Docker Hub
- Ubuntu GitHub-hosted runner

## Pipeline flow
1. **Source control:** A push or pull request targeting `main` triggers the workflow.
2. **Build:** GitHub Actions checks out the repository, sets up Python 3.11, and installs dependencies.
3. **Test:** Pytest executes the application's unit tests. The Docker job depends on this job, so an unsuccessful test prevents image publishing.
4. **Docker build and publish:** On a push to `main`, the workflow builds the Docker image and pushes it to Docker Hub. The Git commit SHA is used as the image tag.
5. **Completion:** The final job reports successful completion. For pull requests, Docker publishing is intentionally skipped.

## Repository structure
```text
.github/workflows/cicd.yml
app/app.py
tests/test_app.py
requirements.txt
Dockerfile
README.md
```

## GitHub repository secrets
Before pushing to `main`, create these repository Actions secrets:

| Secret | Description |
|---|---|
| `DOCKERHUB_USERNAME` | Docker Hub username |
| `DOCKERHUB_TOKEN` | Docker Hub access token |

Create a Docker Hub access token in Docker Hub account settings. Do not put credentials directly in the workflow or source code.

The workflow publishes the image as:
`DOCKERHUB_USERNAME/devops-demo:GITHUB_COMMIT_SHA`

Create the `devops-demo` repository in Docker Hub before running the workflow, if your Docker Hub account requires the repository to exist.

## Run locally
```bash
python -m venv .venv
# Linux/macOS:
source .venv/bin/activate
# Windows PowerShell:
# .venv\Scripts\Activate.ps1

pip install -r requirements.txt
python -m pytest tests/ -v
python app/app.py
```

Open `http://localhost:5000/` or `http://localhost:5000/health`.

## Build and run the Docker image locally
```bash
docker build -t devops-demo:local .
docker run --rm -p 5000:5000 devops-demo:local
```

## Expected result
- The build job installs the application dependencies.
- The test job reports both unit tests as passed.
- On a push to `main`, the Docker job builds and pushes an image tagged with the commit SHA.
- The workflow run is visible under the repository's **Actions** tab.

## Notes
This is a demonstration pipeline. It publishes the container image to Docker Hub; it does not deploy the application to a production server or Kubernetes cluster.
