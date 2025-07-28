```
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt

COPY . .

CMD ["python", "app.py"]
```

---

## **Build and Push Docker Image to DockerHub**

```yaml
name: Build and Push Docker Image

on:
  push:
    branches: [main]

env:
  REGISTRY: docker.io
  IMAGE_NAME: yourdockerhubusername/myapp

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest

```

---

## **GitHub Secrets Required**

In your repo settings, under **Settings > Secrets and Variables > Actions**, add:

- `DOCKERHUB_USERNAME`
- `DOCKERHUB_TOKEN` (get this from DockerHub [https://hub.docker.com/settings/security])

---

## **Tagging Strategy (Optional Advanced)**

You can automatically tag your images based on commit or Git tag:

```yaml
tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
```

Other options:

- `${{ github.ref_name }}` for branch name
- `${{ github.run_number }}` for build ID

---

## **Security Best Practices**

- Never hardcode credentials — use GitHub Secrets.
- Use `least privilege` DockerHub tokens.
- Don't build untrusted PRs without validation.
