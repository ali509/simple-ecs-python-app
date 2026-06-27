# Simple Python App for ECS

![CI/CD](https://github.com/ali509/simple-ecs-python-app/actions/workflows/deploy.yml/badge.svg)

This repository contains a minimal Flask application that exposes:
- `/` returning a JSON message
- `/health` returning a health check response

## Local development

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pytest -q
python app.py
```

## Container build

```bash
docker build -t simple-ecs-app .
docker run -p 8000:8000 simple-ecs-app
```

This is intended to be used as a simple target for ECS deployment and GitHub Actions-based testing. The repository also includes a deployment workflow that builds and publishes a container image to GitHub Container Registry.
