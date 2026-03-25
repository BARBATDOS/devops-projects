# 🚀 DevOps Project 1: EC2 + Nginx Deployment

## 📌 Overview

This project demonstrates the setup of a cloud-based web server using AWS EC2 and Nginx, along with version control using Git and GitHub. It also includes a basic deployment workflow.

---

## 🧠 What I Did

### 1. AWS EC2 Setup

* Launched an Ubuntu EC2 instance (t3.micro)
* Configured security groups (SSH - 22, HTTP - 80)
* Connected via SSH using:

  * PowerShell
  * WSL

---

### 2. Linux Exploration

* Navigated filesystem (`pwd`, `ls`, `cd`)
* Checked system info (`top`, `df -h`, `free -h`)
* Understood basic Linux structure

---

### 3. Nginx Setup

* Installed Nginx
* Started and enabled service
* Verified running service using:

  * `systemctl`
  * `ss -tulnp`
* Accessed default webpage via public IP

---

### 4. Custom Web Page Deployment

* Edited HTML file
* Replaced default Nginx page
* Reloaded Nginx service
* Verified live changes in browser

---

### 5. Project Structure

Created a structured development workflow:

/home/ubuntu/Devops-projects/
└── Project1-EC2-Nginx/
└── index.html

* Kept development files separate from production (`/var/www/html`)

---

### 6. Git & GitHub Setup

* Initialized Git repository
* Configured global username and email
* Changed default branch to `main`
* Created GitHub repository
* Pushed code using Personal Access Token (PAT)

---

### 7. Deployment Script (Manual CI/CD)

Created a deployment script:

deploy.sh:

* Pull latest code from GitHub
* Copy files to `/var/www/html`
* Reload Nginx

---

## 🔁 Deployment Workflow

1. Edit code
2. `git add .`
3. `git commit -m "message"`
4. `git push`
5. Run `deploy.sh` on EC2

---

## 🧪 Key Learnings

* SSH and remote server access
* Linux file system and permissions
* Web server setup (Nginx)
* Git workflow (add, commit, push, pull)
* Debugging real issues (SSH keys, paths, permissions)
* Basic deployment automation

---

## 🚧 Next Steps

* Automate deployment using GitHub Actions
* Learn Docker and containerization
* Set up domain + HTTPS (SSL)
* Implement monitoring/logging

---

## 📌 Notes

This project focuses on understanding fundamentals rather than using pre-built tools or copying commands.

---

## 👨‍💻 Author

Yoseph Shekatkar

