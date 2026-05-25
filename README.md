# 🚀 OpsForge — opsforge.top

A personal DevOps portfolio site documenting my transition from technical support into DevOps engineering. Every architecture decision here is intentional — built, broken, learned from, and rebuilt better.

---

## 📌 Overview

This project started as a simple static site deployed on AWS EC2 with Nginx. It evolved into a fully serverless architecture on S3 + CloudFront after a real-world cost problem forced a better infrastructure decision.

That's not a failure story. That's how DevOps engineers actually learn.

---

## 🏗️ Architecture — Current (v3.0)

```
User
 └── opsforge.top (Porkbun ALIAS record)
      └── CloudFront CDN (HTTPS, HTTP/2, global edge)
           └── ACM Certificate (auto-renewing SSL, free forever)
                └── S3 Bucket (static files)
```

**Monthly cost: $0**

| Service | Provider | Cost |
|---|---|---|
| Domain | Porkbun | ~$4/year (already owned) |
| DNS | Porkbun | Free |
| SSL Certificate | AWS ACM | Free forever |
| CDN | AWS CloudFront | Free tier (1TB/month) |
| Storage | AWS S3 | Free tier (5GB) |

---

## 📜 Architecture History — Why It Changed

### v1.0 — v2.0: EC2 + Nginx (March–May 2026)

The original setup was a deliberate learning exercise — deploy a real server, configure it manually, understand every layer.

```
User
 └── opsforge.top (Porkbun A record)
      └── Elastic IP (static public IP)
           └── AWS EC2 t3.micro (Ubuntu)
                └── Nginx (reverse proxy + SSL termination)
                     └── Let's Encrypt / Certbot (SSL)
```

**What it cost:**
| Month | Cost |
|---|---|
| March 2026 | $2.06 |
| April 2026 | $7.22 |
| May 2026 (partial) | ~$1.00 |
| **Total** | **~$10.28** |

**What went wrong:**

In April, AWS Cost Anomaly Detection flagged a spike between April 5–7 — actual spend was $0.70 over 3 days against an expected $0.06, representing a 1066% increase above normal. The root cause was the Elastic IP becoming detached from the running instance during that period. AWS charges ~$0.005/hour for an idle, unattached Elastic IP — small per hour, significant when compounding. Combined with EC2 running 24/7 at ~$7/month after free tier, the costs were unsustainable for a portfolio site with no revenue.

**The decision:**

Terminate everything. Rebuild on infrastructure that is free permanently — not just during the AWS 12-month free tier window.

> This is a real lesson in cloud cost management: always understand which AWS free tier benefits are time-limited (EC2, RDS) versus permanently free (Lambda, CloudFront, ACM, DynamoDB). Build portfolios and personal projects on the permanently free tier wherever possible.

---

### v3.0: S3 + CloudFront (May 2026 — present)

The site is purely static — HTML, CSS, no backend. EC2 was never the right tool for this. S3 + CloudFront is the industry-standard architecture for static sites and has several advantages over the old setup:

| | EC2 + Nginx | S3 + CloudFront |
|---|---|---|
| Cost | ~$7-8/month | $0 permanently |
| SSL | Let's Encrypt (manual renewal) | ACM (auto-renewing, forever free) |
| Performance | Single region | Global CDN edge nodes |
| Maintenance | OS updates, Nginx config, security patches | Zero maintenance |
| Availability | Single instance, single AZ | Globally distributed |
| Scalability | Manual | Automatic |

The only trade-off: no server-side logic. For a static portfolio, that's not a trade-off at all.

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| Hosting | AWS S3 (static website hosting) |
| CDN | AWS CloudFront |
| SSL | AWS ACM (Certificate Manager) |
| DNS | Porkbun (ALIAS + CNAME records) |
| Domain | opsforge.top |
| Deployment | AWS CLI from WSL (Ubuntu 24.04) |

---

## 🔧 Setup — How It Was Built

### 1. S3 Bucket
```bash
# Create bucket
aws s3 mb s3://opsforge.top --region us-east-1

# Upload site files
aws s3 cp index.html s3://opsforge.top/

# Enable static website hosting
aws s3 website s3://opsforge.top/ \
  --index-document index.html \
  --error-document index.html

# Remove public access block (intentional — public website)
aws s3api delete-public-access-block --bucket opsforge.top

# Apply public read policy
aws s3api put-bucket-policy --bucket opsforge.top --policy '{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": "*",
    "Action": "s3:GetObject",
    "Resource": "arn:aws:s3:::opsforge.top/*"
  }]
}'
```

### 2. SSL Certificate via ACM
```bash
# Request certificate — must be us-east-1 for CloudFront
aws acm request-certificate \
  --domain-name opsforge.top \
  --subject-alternative-names www.opsforge.top \
  --validation-method DNS \
  --region us-east-1
```
DNS validation records added to Porkbun. Certificate issued automatically within 15 minutes.

### 3. CloudFront Distribution
```bash
aws cloudfront create-distribution \
  --distribution-config '{
    "Origins": { ... "DomainName": "opsforge.top.s3-website-us-east-1.amazonaws.com" },
    "DefaultCacheBehavior": { "ViewerProtocolPolicy": "redirect-to-https" },
    "Aliases": { "Items": ["opsforge.top", "www.opsforge.top"] },
    "ViewerCertificate": { "ACMCertificateArn": "...", "SSLSupportMethod": "sni-only" }
  }'
```

### 4. DNS Configuration (Porkbun)
| Type | Host | Value |
|---|---|---|
| ALIAS | @ | dgma4hvuf08ee.cloudfront.net |
| CNAME | www | dgma4hvuf08ee.cloudfront.net |

> Note: CNAME on root domain violates RFC standards. Porkbun automatically suggested ALIAS record type instead — functionally identical to CNAME but compliant on root domains.

---

## 🗺️ Roadmap — What's Being Built Next

| # | Phase | Status |
|---|---|---|
| 01 | Linux + Bash Scripting | 🔄 In Progress |
| 02 | Networking Fundamentals | ⏳ Up Next |
| 03 | AWS — CCP + SAA Certifications | 📖 Studying |
| 04 | Git + GitHub | ⏳ Week 2 |
| 05 | Docker + CI/CD | ⏳ Coming Soon |
| 06 | Jenkins + GitHub Actions | ⏳ Coming Soon |
| 07 | Kubernetes | ⏳ Coming Soon |

---

## 🔐 Security Notes

- AWS root account MFA enabled
- IAM user with scoped permissions used for CLI access (not root)
- S3 Block Public Access disabled intentionally for this bucket only
- CloudFront enforces HTTPS — all HTTP redirected to HTTPS
- TLS 1.2 minimum enforced on CloudFront
- ACM certificate covers both apex and www subdomains

---

## 👨‍💻 Author

**Yoseph — OpsForge**
Transitioning into DevOps | AWS CCP → SAA in progress
GitHub: [BARBATDOS](https://github.com/BARBATDOS)
