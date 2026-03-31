# 🚀 DevOps Project 1: EC2 + Nginx Deployment

## 🌍 Live Demo

Application is accessible at:
http://52.6.51.124

---

## 📌 Overview

This project demonstrates provisioning a cloud-based Linux server on AWS, configuring a web server (Nginx), and implementing a deployment workflow using Git and shell scripting.

It simulates a real-world scenario of hosting and updating a web application on a remote server.

---

## 🧠 What I Did

### 1. AWS EC2 Setup

* Launched an Ubuntu EC2 instance (t3.micro)
* Configured security groups:

  * SSH (Port 22)
  * HTTP (Port 80)
* Connected via SSH using PowerShell and WSL

---

### 🌐 Elastic IP Configuration

* Allocated and attached an Elastic IP
* Ensures a **static public IP address** even after instance restart
* Used for consistent browser access and SSH connectivity

**Elastic IP:** 52.6.51.124

---

### 2. Linux Exploration

* Navigated filesystem (`pwd`, `ls`, `cd`)
* Checked system resources (`top`, `df -h`, `free -h`)
* Understood Linux directory structure

---

### 3. Nginx Setup

* Installed and configured Nginx as a web server
* Started and enabled the service
* Verified using:

  * `systemctl`
  * `ss -tulnp`
* Accessed application via browser

---

### 4. Custom Web Page Deployment

* Created and modified HTML content
* Replaced default Nginx page
* Reloaded Nginx to apply changes
* Verified updates live

---

### 5. Project Structure

/home/ubuntu/Devops-projects/
└── Project1-EC2-Nginx/
  └── index.html

* Maintained separation between development and production (`/var/www/html`)

---

### 6. Git & GitHub Setup

* Initialized Git repository
* Configured user identity
* Set default branch to `main`
* Created GitHub repository
* Pushed code using Personal Access Token

---

### 7. Deployment Script (Manual CI/CD)

Created `deploy.sh` script to:

* Pull latest code from GitHub
* Copy files to `/var/www/html`
* Reload Nginx

---

## ⚙️ Deployment Workflow

1. Update code locally
2. Push to GitHub
3. SSH into EC2
4. Run:

   ```
   ./deploy.sh
   ```

---

## 🧪 Key Learnings

* AWS EC2 provisioning and management
* Nginx web server configuration
* Git-based deployment workflow
* Shell scripting for automation
* Real-world debugging and troubleshooting

---

## 🚧 Next Steps

* Automate deployment using GitHub Actions
* Add custom domain using Route 53
* Enable HTTPS with SSL
* Containerize using Docker

---

## 📌 Notes

This project focuses on building foundational DevOps skills by manually configuring infrastructure and deployment processes.

---

## 👨‍💻 Author

Yoseph Shekatkar

