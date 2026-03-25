# 🚀 Reusable Docker Deploy Template (GitHub Actions)

This repository provides a **GitHub Actions workflow Template** to build, push, and deploy Docker-based applications to a VPS using Docker Compose.

It is designed to be **stack-agnostic** — as long as your project uses a `Dockerfile` and/or `docker-compose.yml`, this template will work.

---

# ✨ Features

* 🔁 Reusable workflow (`workflow_call`)
* 🐳 Build & push Docker image to GHCR
* 🚀 Deploy to VPS via SSH
* 🔒 Secure authentication (PAT for GHCR)
* ⚡ Concurrency control (prevent overlapping deploys)
* 🧪 Basic container health check
* 🧹 Automatic Docker cleanup

---

# 📦 Requirements

## 1. Project Requirements

Your project repository must include:

* `Dockerfile` **or**
* `docker-compose.yml` (recommended)

---

## 2. VPS Requirements

Make sure your VPS has:

* Docker installed
* Docker Compose installed
* A prepared app directory (e.g. `/var/www/my-app`)
* Proper permissions for the SSH user

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

jobs:
  deploy:
    uses: muhammadghifar/template-deployment/.github/workflows/deploy.yml@v1    # adjust the version as needed as available

    with:
      app_dir: /var/www/my-app   # optional, will use default value if not specified 

    secrets:
      VPS_HOST: ${{ secrets.VPS_HOST }}
      VPS_USER: ${{ secrets.VPS_USER }}
      VPS_SSH_KEY: ${{ secrets.VPS_SSH_KEY }}
      GHCR_PAT: ${{ secrets.GHCR_PAT }}
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

* Image name:

  ```
  ghcr.io/<username>/<repo-name>
  ```

* Default app directory:

  ```
  /var/www/<repo-name>
  ```

---

# ⚠️ Notes

* The template **does NOT create directories automatically**
* Ensure your VPS directory exists before deploying
* Deployment will fail if:

  * directory is missing
  * container fails to start

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

* Rollback on failure
* SHA-based deployment (instead of latest)
* Multi-environment support (dev/staging/prod)
* Healthcheck via HTTP endpoint
* Multi-service detection

---

# 📄 License

MIT (or adjust as needed)

---

# 🤝 Contribution

Feel free to fork and improve this template to fit your workflow.
