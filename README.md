# Django AWS Deployment with GitHub Actions 🚀

This repository demonstrates how to deploy a **Django** project to **AWS EC2** using **GitHub Actions** for Continuous Integration and Continuous Deployment (CI/CD).

## 🔧 Tech Stack
- Django (Python Web Framework)
- GitHub Actions (CI/CD)
- AWS EC2 (Ubuntu Server)
- Nginx + Gunicorn (Production Setup)
- PostgreSQL or SQLite (Database)

---

## 📁 Project Structure
```
├── .github/workflows/
│   └── deploy.yml           # GitHub Actions workflow for deployment
├── myproject/               # Django project directory
│   ├── settings.py
│   └── ...
├── manage.py
├── requirements.txt
└── README.md
```

---

## ⚙️ Setup on AWS EC2

1. Launch an EC2 instance (Ubuntu)
2. Install Python, pip, virtualenv, PostgreSQL (optional), Nginx, and Git
3. Setup SSH access for GitHub Actions
4. Clone your repo and configure environment

---

## 🔐 GitHub Secrets Required

In your GitHub repo, go to **Settings > Secrets and variables > Actions** and add:

- `EC2_HOST` – Your EC2 public IP or domain
- `EC2_USER` – Typically `ubuntu`
- `EC2_KEY` – Your private SSH key (for connecting to EC2)
- `EC2_PORT` – Usually `22`

---

## 🚀 CI/CD Workflow Overview

Every push to the `main` branch triggers the following:

1. GitHub Actions installs dependencies
2. SSH into EC2
3. Pulls latest code
4. Installs Python dependencies
5. Runs Django migrations
6. Collects static files
7. Restarts Gunicorn / Nginx

---

## 🧪 Local Development

```bash
# 1. Clone the repository
git clone https://github.com/your-username/django-aws-githubactions-deployment.git
cd django-aws-githubactions-deployment

# 2. Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run server
python manage.py runserver
```

Visit `http://127.0.0.1:8000` to view the app locally.

---

## 📄 Example GitHub Actions Workflow (deploy.yml)

```yaml
name: Deploy Django App to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up SSH
        uses: webfactory/ssh-agent@v0.7.0
        with:
          ssh-private-key: ${{ secrets.EC2_KEY }}

      - name: Deploy to EC2
        run: |
          ssh -o StrictHostKeyChecking=no ${{ secrets.EC2_USER }}@${{ secrets.EC2_HOST }} -p ${{ secrets.EC2_PORT }} << 'EOF'
            cd /home/ubuntu/your-project-dir
            git pull origin main
            source venv/bin/activate
            pip install -r requirements.txt
            python manage.py migrate
            python manage.py collectstatic --noinput
            sudo systemctl restart gunicorn
            sudo systemctl restart nginx
          EOF
```

---

## 📌 Notes

- Make sure your EC2 instance has proper security group rules (allow SSH, HTTP/HTTPS)
- Replace `your-project-dir` with the actual directory on the server
- Ensure `gunicorn` and `nginx` are configured correctly on the EC2 instance

---

## 👤 Author

**Krishna Kumar K P**  
🔗 [LinkedIn](https://www.linkedin.com/in/krishna-kumar-kondooru-731044256/)  
⭐ *Star this repo if you find it helpful!*

