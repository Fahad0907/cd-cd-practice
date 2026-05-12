# CI/CD with GitHub Actions and AWS EC2

A simple learning project showing how to deploy a Django application automatically to an AWS EC2 Ubuntu server using GitHub Actions and SSH.

---

## 🚀 Technologies Used

- GitHub Actions
- AWS EC2
- Ubuntu Server
- Git
- Django
- SSH

---

# 📁 Project Structure

```bash
.github/
└── workflows/
    └── deploy.yml
```

---

# ⚙️ Step 1: Create EC2 Instance

Create an Ubuntu EC2 instance from AWS and connect using SSH.

```bash
ssh -i your-key.pem ubuntu@YOUR_EC2_PUBLIC_IP
```

---

# ⚙️ Step 2: Install Git on EC2

```bash
sudo apt update
sudo apt install git -y
```

---

# ⚙️ Step 3: Create Project Directory

```bash
sudo mkdir -p /var/www
sudo chown -R ubuntu:ubuntu /var/www

cd /var/www
```

Clone your repository:

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
```

---

# ⚙️ Step 4: Generate SSH Key for GitHub Actions

Run on EC2 server:

```bash
ssh-keygen -t ed25519 -C "github-actions-deploy"
```

This creates:

```bash
~/.ssh/github-actions-deploy
~/.ssh/github-actions-deploy.pub
```

---

# ⚙️ Step 5: Add Public Key to Authorized Keys

```bash
cat ~/.ssh/github-actions-deploy.pub >> ~/.ssh/authorized_keys
```

Set proper permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

# ⚙️ Step 6: Add GitHub Secret

Go to:

```text
GitHub Repository → Settings → Secrets and variables → Actions
```

Create a new repository secret:

| Secret Name | Value |
|---|---|
| EC2_PRIVATE | Contents of github-actions-deploy private key |

Get private key:

```bash
cat ~/.ssh/github-actions-deploy
```

Copy everything including:

```text
-----BEGIN OPENSSH PRIVATE KEY-----
```

and

```text
-----END OPENSSH PRIVATE KEY-----
```

---

# ⚙️ Step 7: Create GitHub Actions Workflow

Create file:

```bash
.github/workflows/deploy.yml
```

Add the following workflow:

```yaml
name: Deploy Django App

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      # Checkout repository
      - name: Checkout Code
        uses: actions/checkout@v3

      # Configure SSH
      - name: Set up SSH
        run: |
          mkdir -p ~/.ssh
          echo "${{ secrets.EC2_PRIVATE }}" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          chmod 700 ~/.ssh
          ssh-keyscan -H YOUR_EC2_PUBLIC_IP >> ~/.ssh/known_hosts

      # Start ssh-agent
      - name: Set up ssh-agent
        run: |
          eval "$(ssh-agent -s)"
          ssh-add ~/.ssh/id_rsa

      # Deploy application
      - name: Deploy to EC2
        run: |
          ssh -i ~/.ssh/id_rsa ubuntu@YOUR_EC2_PUBLIC_IP << 'EOF'

            cd /var/www/YOUR_PROJECT_NAME

            git pull origin main

          EOF

      # Success message
      - name: Notify Success
        run: echo "Deployment Successful!"
```

---

# ⚙️ Step 8: Push Code

```bash
git add .
git commit -m "add github actions ci/cd"
git push origin main
```

---

# ⚙️ Step 9: Check GitHub Actions

Go to:

```text
GitHub Repository → Actions
```

You will see the deployment running automatically after every push to the `main` branch.

---

# 🔄 Deployment Flow

```text
Developer Pushes Code
        ↓
GitHub Actions Triggered
        ↓
SSH Connection to EC2
        ↓
Pull Latest Code
        ↓
Application Updated
```

---

# 📚 Learning Concepts

This project demonstrates:

- CI/CD Basics
- GitHub Actions
- SSH Authentication
- GitHub Secrets
- AWS EC2 Deployment
- Remote Server Automation

---

# 🚀 Future Improvements

- Docker Deployment
- Nginx Reverse Proxy
- SSL with Let's Encrypt
- Gunicorn Setup
- Zero Downtime Deployment
- AWS ECS & ECR

---

# 👨‍💻 Author

Fakhrul Islam Fahad

```
change
