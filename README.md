# 🚀 Reusable Docker Deploy Template (GitHub Actions)

This repository provides a **GitHub Actions workflow Template** to build, push, and deploy Docker-based applications to a VPS using Docker Compose.

It is designed to be **stack-agnostic** — as long as your project uses a `Dockerfile` and/or `docker-compose.yml`, this template will work.

---

# ✨ Features

- 🔁 Reusable workflow (`workflow_call`)
- 🐳 Build & push Docker image to GHCR
- 🚀 Deploy to VPS via SSH
- 🔒 Secure authentication (PAT for GHCR)
- ⚡ Concurrency control (prevent overlapping deploys)
- 🧪 Basic container health check
- 🧹 Automatic Docker cleanup

---

# 📦 Requirements

## 1. Project Requirements

Your project repository must include:

- `Dockerfile` **or**
- `docker-compose.yml` (recommended)

---

## 2. VPS Requirements

Make sure your VPS has:

- Docker installed
- Docker Compose installed
- A prepared app directory (e.g. `/var/www/my-app`)
- Proper permissions for the SSH user

Example:

```bash
/var/www/my-app/
  └── docker-compose.yml
```

---

## 3. GitHub Secrets (in Project Repo)

You must configure the following secrets in your **project repository**:

```
VPS_HOST        # VPS IP / domain
VPS_USER        # SSH user
VPS_SSH_KEY     # Private SSH key
GHCR_PAT        # GitHub Personal Access Token (read:packages)
```

---

# 🔐 GHCR Personal Access Token

Create a token with:

```
read:packages
```

Then store it as:

```
GHCR_PAT
```

---

# 🛠️ Usage

## 1. Use the Workflow Template

Create a workflow in your **project repository**:

```yaml
name: Deploy My App

on:
  push:
    branches: [main]

permissions:
  contents: read
  packages: write # 👈 REQUIRED for pushing to GHCR

jobs:
  deploy:
    uses: muhammadghifar/template-deployment/.github/workflows/deploy.yml@v1 # adjust the version as needed as available

    # If your project requires environment variables during build time (e.g. Next.js or Vite), you can pass them using `build_args`.
    # for example
    with:
      build_args: |
        NEXT_PUBLIC_API_URL=${{ secrets.NEXT_PUBLIC_API_URL }}

    secrets:
      VPS_HOST: ${{ secrets.VPS_HOST }}
      VPS_USER: ${{ secrets.VPS_USER }}
      VPS_SSH_KEY: ${{ secrets.VPS_SSH_KEY }}
      GHCR_PAT: ${{ secrets.GHCR_PAT }}
      APP_DIR: ${{ secrets.APP_DIR }} # optional, will use default value if not specified
```

---

## 2. Push to Main Branch

```bash
git push origin main
```

This will trigger:

1. Build Docker image
2. Push to GHCR
3. SSH into VPS
4. Pull latest image
5. Restart services via Docker Compose

---

# ⚙️ Default Behavior

- Image name:

  ```
  ghcr.io/<username>/<repo-name>
  ```

* App directory resolution (priority):
  1. `APP_DIR` secret
  2. `/var/www/<repo-name>`

---

# 🔐 Authentication Overview

This workflow uses two different authentication mechanisms:

### 1. GitHub Actions (CI)

Uses built-in `GITHUB_TOKEN`:

- Used for building & pushing Docker images
- Requires: `packages: write` permission

> ⚠️ Important:
> This workflow requires `packages: write` permission in your GitHub Actions workflow.
> Without it, the build step will fail when pushing to GHCR.

### 2. VPS (Deployment)

Uses `GHCR_PAT`:

- Used for `docker pull` on VPS
- Requires: `read:packages` scope only

---

# ⚠️ Notes

- The template **does NOT create directories automatically**
- Ensure your VPS directory exists before deploying
- Deployment will fail if:
  - directory is missing
  - container fails to start

---

# 🧠 How It Works

```
Push to project repo
        ↓
GitHub Actions (project)
        ↓
Calls reusable workflow (this repo)
        ↓
Build & push image to GHCR
        ↓
SSH to VPS
        ↓
docker compose pull + up
```

---

# 🚀 Future Improvements (Optional)

- Rollback on failure
- SHA-based deployment (instead of latest)
- Multi-environment support (dev/staging/prod)
- Healthcheck via HTTP endpoint
- Multi-service detection

---

# 📄 License

MIT (or adjust as needed)

---

# 🤝 Contribution

Feel free to fork and improve this template to fit your workflow.
