# 🚀 Docker + Ansible + GitHub Actions CI/CD Pipeline

A Flask app deployed to a remote server via Docker and Ansible, triggered by GitHub Actions on every push to `main`.

## Setup

### 1. GitHub Secrets

Go to your repository **Settings → Secrets and variables → Actions** and add the following secrets:

| Secret        | Description                            |
|---------------|----------------------------------------|
| `DOCKER_USER` | Your Docker Hub username               |
| `DOCKER_PASS` | Your Docker Hub password or token      |
| `SSH_KEY`     | Private SSH key for the target server  |
| `DOCKER_IMAGE`| Full Docker image name (e.g. `youruser/`) |

### 2. Update inventory.ini

Edit `ansible/inventory.ini` and replace `<REAL_SERVER_IP>` with your actual server's IP address:

```ini
[web]
192.168.1.100 ansible_user=ubuntu
```

### 3. Pre-authorize SSH Key on the Target Server

Generate an SSH key pair (if you don't have one):

```bash
ssh-keygen -t ed25519 -C "github-actions-ci"
```

Copy the public key to the target server:

```bash
ssh-copy-id ubuntu@<REAL_SERVER_IP>
```

Then add the **private** key (`id_ed25519`) to the `SSH_KEY` GitHub secret.

### 4. Trigger the Pipeline

Push to the `main` branch:

```bash
git add . && git commit -m "Deploy" && git push origin main
```

The pipeline will:
1. Checkout the code
2. Log in to Docker Hub
3. Build and push the Docker image
4. Install Ansible
5. SSH into the server and run the playbook (install Docker, remove old container, start new one)

## File Structure

```
project/
├── app/
│   ├── app.py
│   └── requirements.txt
├── Dockerfile
├── ansible/
│   ├── inventory.ini
│   └── deploy.yml
├── .github/workflows/
│   └── ci-cd.yml
└── README.md
```
