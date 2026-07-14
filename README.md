# 🚀 OpsForge — opsforge.top

A personal DevOps portfolio site documenting my transition from technical support into DevOps engineering. Every architecture decision here is intentional — built, broken, learned from, and rebuilt better.

---

## 📌 Overview

This project started as a simple static site deployed on AWS EC2 with Nginx. It evolved into a fully serverless architecture on S3 + CloudFront after a real-world cost problem forced a better infrastructure decision. In July 2026, it moved again — off AWS entirely, onto Cloudflare Pages — this time not because of a cost problem, but because of a real, time-boxed deadline on the AWS account itself.

None of these are failure stories. That's how DevOps engineers actually learn: something forces a decision, you diagnose it properly, and you document why.

---

## 🏗️ Architecture — Current (v4.0)

```
User
 └── opsforge.top (Cloudflare nameservers)
      └── Cloudflare edge network (HTTPS, global, proxied)
           └── Cloudflare Pages (static hosting)
```

**Monthly cost: $0 — and no time-boxed credits, no card on file, no dependency on a promotional trial.**

| Service         | Provider          | Cost                     |
| ---------------- | ------------------ | ------------------------- |
| Domain           | Porkbun            | ~$4/year (already owned) |
| DNS              | Cloudflare         | Free                     |
| SSL Certificate  | Cloudflare (Google Trust Services) | Free forever |
| CDN + Hosting    | Cloudflare Pages   | Free (100k req/day)      |

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

| Month              | Cost        |
| ------------------ | ----------- |
| March 2026         | $2.06       |
| April 2026         | $7.22       |
| May 2026 (partial) | ~$1.00      |
| **Total**          | **~$10.28** |

**What went wrong:**

In April, AWS Cost Anomaly Detection flagged a spike between April 5–7 — actual spend was $0.70 over 3 days against an expected $0.06, representing a 1066% increase above normal. The root cause was the Elastic IP becoming detached from the running instance during that period. AWS charges ~$0.005/hour for an idle, unattached Elastic IP — small per hour, significant when compounding. Combined with EC2 running 24/7 at ~$7/month after free tier, the costs were unsustainable for a portfolio site with no revenue.

**The decision:**

Terminate everything. Rebuild on infrastructure that is free permanently — not just during the AWS 12-month free tier window.
> This is a real lesson in cloud cost management: always understand which AWS free tier benefits are time-limited (EC2, RDS) versus permanently free (Lambda, CloudFront, ACM, DynamoDB). Build portfolios and personal projects on the permanently free tier wherever possible.

---

### v3.0: S3 + CloudFront (May–July 2026)

The site was purely static — HTML, CSS, no backend. EC2 was never the right tool for this. S3 + CloudFront was the industry-standard architecture for static sites, and a real upgrade over the old setup:

|              | EC2 + Nginx                                | S3 + CloudFront                   |
| ------------ | ------------------------------------------ | --------------------------------- |
| Cost         | ~$7-8/month                                | $0 permanently\*                  |
| SSL          | Let's Encrypt (manual renewal)             | ACM (auto-renewing, forever free) |
| Performance  | Single region                              | Global CDN edge nodes             |
| Maintenance  | OS updates, Nginx config, security patches | Zero maintenance                  |
| Availability | Single instance, single AZ                 | Globally distributed              |
| Scalability  | Manual                                     | Automatic                         |

\*This assumption turned out to be incomplete — see below.

**What forced the next move:**

This time it wasn't a cost anomaly — S3 and CloudFront usage stayed genuinely near $0. The problem was one level up: the **AWS account itself** was running on a time-boxed free-credit program (~$128 in credits, a hard ~22-day countdown), not a permanent arrangement. The account's own dashboard was explicit: *"Your access to AWS services will end when credits are depleted."* Every service built on top of that account — regardless of how cheap it individually was — was exposed to that same deadline.

There was also a smaller, unrelated loose end never fully closed out: a recurring `EC2 - Other` charge (~$0.28/month) that had been showing up since March, most likely an orphaned Elastic IP or EBS volume left over from the original v1.0 instance. Small, but a good reminder that "terminate the instance" and "fully clean up every resource it created" aren't automatically the same thing.

**The decision:**

Migrate off AWS entirely — including the parts that had genuinely been free — rather than wait for a countdown to force it under pressure later. Cloudflare Pages was the replacement: genuinely free tier, no card required, no time-boxed credit program, no billing account dependency at all.

---

### v4.0: Cloudflare Pages (July 2026 — present)

**Real migration notes, not a clean tutorial writeup** — this one had a couple of genuine diagnostic detours worth documenting, not skipping over:

**1. Nameserver rejection, misdiagnosed at first.** Manually pointing Porkbun's nameservers at Cloudflare's failed with a generic "invalid nameservers" error, no further detail. The actual cause turned out to be **Domain Lock** — a security feature on the Porkbun account (correctly) blocking exactly this kind of change to prevent unauthorized hijacking. Disabling it first, then retrying with the same nameservers, worked immediately.

**2. Stale DNS records blocking domain attachment — twice.** After the nameserver handoff succeeded, connecting the domain to the new Cloudflare Pages project failed with `Hostname already has externally managed DNS records`. Cloudflare's own "automatic import" (a safety-net snapshot of the old CloudFront-pointing A/AAAA/CNAME records, done during onboarding) was still sitting there, conflicting with the new connection. Solved by deleting those imported records — the root domain's 24 old CloudFront IP records first, then a lingering `www` CNAME the same way, since it was a separate hostname that hit the identical error independently.

**3. A near-miss with Porkbun's own DNS panel.** Porkbun has a separate, built-in "DNS Powered by Cloudflare" feature — cosmetically similar to a real account-level Cloudflare connection, but not the same thing. After the migration succeeded, that panel (now orphaned, since real DNS control had already moved to Cloudflare) prompted *"Would you like us to update your domain to use our nameservers?"* — clicking yes would have silently reverted the entire migration back to Porkbun's own DNS. Caught before clicking, but a good example of a UI that looks helpful while pointing in exactly the wrong direction.

**Result:** zero downtime throughout — the old nameservers kept serving traffic correctly the entire time the new ones were propagating, so the live site was never actually down during any of this.

```
User
 └── opsforge.top (Cloudflare nameservers: ullis.ns.cloudflare.com, yisroel.ns.cloudflare.com)
      └── Cloudflare edge network
           └── Cloudflare Pages (direct file upload, no build step)
```

---

## ⚙️ Tech Stack

| Layer      | Technology                      |
| ---------- | -------------------------------- |
| Hosting    | Cloudflare Pages                 |
| CDN + DNS  | Cloudflare (proxied)             |
| SSL        | Cloudflare, via Google Trust Services |
| Domain     | opsforge.top (registrar: Porkbun) |
| Deployment | Direct file upload via Cloudflare dashboard |

---

## 🔧 Setup — How It Was Built (v4.0)

### 1. Cloudflare Pages project

Created via **Workers & Pages → Create → Pages → Upload assets** — direct file upload, no Git connection needed for a single static file with no build step. Deployed first to a temporary `*.pages.dev` URL and verified working before touching any DNS, to keep the live site's risk at zero during testing.

### 2. Connect the domain

**Workers & Pages → Domains → Add domain** and select the zone. Cloudflare requires the domain to already be an active zone in the account (see step 3) before this will succeed.

### 3. Add the domain as a Cloudflare zone

**Domains → Add domain** → `opsforge.top` → Free plan → accept the automatic DNS import (a safety snapshot, not a live connection) → Cloudflare provides two nameservers.

### 4. Update nameservers at the registrar (Porkbun)

**Before this step:** check **Domain Lock** is switched off — otherwise the change is silently rejected.

Then, in the domain's **Nameservers** section (not the DNS records page — a separate section), replace the four existing `*.porkbun.com` nameservers with the two Cloudflare ones:

```
ullis.ns.cloudflare.com
yisroel.ns.cloudflare.com
```

Propagation is typically much faster than the worst-case "up to 48 hours" warning — this one completed in under an hour.

### 5. Clean up the imported DNS snapshot

Once the zone is confirmed active in Cloudflare, delete the old CloudFront-pointing A/AAAA/CNAME records that were imported as a safety net in step 3 — they're not needed once Cloudflare Pages is serving the domain directly, and Cloudflare won't finish attaching the domain to the Pages project while they're still present.

### 6. Decommission the old AWS resources

Once the new domain is confirmed working end-to-end (checked via the SSL certificate issuer — Cloudflare's shows "Google Trust Services," AWS's showed "Amazon"):

- Empty and delete the S3 bucket
- Disable, then delete, the CloudFront distribution (must be disabled first — CloudFront won't allow deleting an active distribution directly)
- Delete the ACM certificate (may need a short wait after the CloudFront deletion finishes propagating before AWS releases the "in use" lock on it)

---

## 🗺️ Roadmap — What's Being Built Next

| #  | Phase                          | Status        |
| --- | ------------------------------ | -------------- |
| 01 | Linux + Bash Scripting         | ✅ Solid       |
| 02 | Networking Fundamentals        | ✅ Solid       |
| 03 | AWS — CCP + SAA Certifications | 🔄 In Progress |
| 04 | Git + GitHub                   | ✅ Solid       |
| 05 | Docker + CI/CD                 | 🔄 In Progress |
| 06 | Jenkins + GitHub Actions       | 🔄 In Progress |
| 07 | Kubernetes                     | ⏳ Queued      |
| 08 | Prometheus + Grafana           | ⏳ Queued      |

---

## 🔐 Security Notes

- Cloudflare proxying enabled — origin (Pages) is not directly exposed, all traffic passes through Cloudflare's network
- HTTPS enforced automatically, TLS certificate auto-provisioned and auto-renewed, no manual certificate management
- No AWS IAM credentials, root account, or billing account required for this project anymore
- Domain registrar (Porkbun) has Domain Lock re-enabled after the nameserver change was completed

---

## 👨‍💻 Author

**Yoseph — OpsForge**
Transitioning into DevOps | AWS CCP → SAA in progress
GitHub: [BARBATDOS](https://github.com/BARBATDOS)
Portfolio: [opsforge.top](https://opsforge.top)
