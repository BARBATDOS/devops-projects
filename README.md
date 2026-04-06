# 🚀 DevOps Project: EC2 + Nginx + Domain + SSL

## 📌 Overview

This project demonstrates a complete deployment of a web application using AWS EC2, Nginx, a custom domain, and SSL (HTTPS).

The goal was to simulate a real-world production setup instead of just running a project locally.

---

## 🧠 What This Project Covers

* Cloud deployment using AWS EC2
* Web server setup using Nginx
* Domain configuration using DNS
* Secure communication using SSL (HTTPS)
* Basic infrastructure understanding

---

## ⚙️ Tech Stack

* **Cloud Provider:** AWS EC2 (t3.micro)
* **Web Server:** Nginx
* **Domain:** opsforge.top
* **SSL:** Let's Encrypt (Certbot)
* **OS:** Ubuntu

---

## 🌐 Live URL

👉 https://opsforge.top

---

## 🏗️ Architecture

User → Domain (DNS) → EC2 Instance → Nginx → Application

---

## 🔧 Setup Steps (High-Level)

### 1. EC2 Setup

* Launched Ubuntu EC2 instance
* Configured security groups (HTTP & HTTPS allowed)

### 2. Nginx Setup

* Installed Nginx
* Configured server block to serve the application

### 3. Domain Configuration

* Purchased domain (opsforge.top)
* Configured DNS (A records) to point to EC2 public IP

### 4. SSL Setup

* Installed Certbot
* Generated SSL certificate using Let's Encrypt
* Enabled HTTPS with auto redirect

---

## 🔐 Security

* HTTPS enabled using SSL certificate
* Secure communication between client and server

---

## 📦 Deployment

* Application deployed manually on EC2
* Nginx used as reverse proxy / web server

---

## 🚀 Future Improvements

* CI/CD pipeline (GitHub → EC2 auto deploy)
* Monitoring & uptime tracking
* Docker containerization
* Load balancing

---

## 👨‍💻 Author

Yoseph (OpsForge)
