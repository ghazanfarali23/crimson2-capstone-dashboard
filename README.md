# Crimson 2 — Harvard Cloud Management Capstone Dashboard

**Group:** Crimson 2 &nbsp;·&nbsp; Ghazanfar · Aaron · Chloe  
**Course:** Advanced Cloud Management — Harvard Extension School, Spring 2026  
**Project:** MedData AWS Migration  

**Live (demo / fork-specific):**  
- Historical static host: [GitHub Pages](https://ghazanfarali23.github.io/crimson2-capstone-dashboard/) (no Cognito gate on older snapshots).  
- **Extra Credit 2:** deploy with **AWS Amplify Hosting** + **Amazon Cognito** and replace the placeholder IDs in `index.html` so users must sign in before the dashboard loads (see below).

---

## About

This dashboard is our **capstone deliverable**, combining all 9 weekly panels plus extra credit into a single interactive web app. Each panel corresponds to a week of the capstone and addresses a different dimension of the MedData cloud migration to AWS.

---

## Authentication (Extra Credit 2 — AWS Cognito)

The SPA uses **AWS Amplify JavaScript v6** loaded from [esm.sh](https://esm.sh) inside a `type="module"` script at the bottom of `index.html`. Only **public** identifiers belong in the repo:

| Constant | Where to find it |
|----------|------------------|
| `COGNITO_USER_POOL_ID` | Cognito → *User pools* → your pool → **User pool ID** (e.g. `us-east-1_AbCdEfGh`) |
| `COGNITO_USER_POOL_CLIENT_ID` | Same pool → *App integration* → **App clients** → **Client ID** (use an app client **without** a client secret for a browser SPA) |
| `COGNITO_REGION` | The region that matches the prefix of the pool ID (e.g. `us-east-1`) |

**Do not** commit IAM user keys, machine credentials, or an app client **secret**. For a hosted single-page app, create a Cognito **hosted UI–style** or standard app client configured for **USER_PASSWORD_AUTH** / **ALLOW_USER_SRP_AUTH** as required by your assignment, and allow self sign-up if you use the built-in sign-up form.

When the constants are left empty, visitors see a **configuration required** message and cannot reach `#app`.

After editing `index.html`, push to the branch connected to **Amplify Hosting** so the live site picks up the change.

---

## Deployment (AWS Amplify Hosting)

This repository is a **pure static** site: one `index.html`, no bundler. Amplify can deploy it with the included **`amplify.yml`**:

- `build` runs a no-op `echo` (Amplify expects a build phase).
- `artifacts.baseDirectory` is **`.`** so the root `index.html` is published.

Behind Amplify, AWS provisions **S3** for objects and **CloudFront** for HTTPS and edge caching—you do not manage those buckets separately for a standard Amplify web app.

**EC2 course deliverable (if assigned separately):** the **EC 2 · Deployment & Ops** panel in the dashboard includes a placeholder for an EC2 public DNS / URL. Replace that text with your instance link or note that submission is only via the course portal.

---

## The 9 Panels

| Panel | Week | Topic | Key Result |
|-------|------|--------|------------|
| **1** | Week 5 | Budget Planning & Cost Estimation | CapEx eliminated: $320K/yr servers · OpEx: $18K/mo AWS · ROI horizon: 3 yrs |
| **2** | Week 6 | ROI & Value Analysis | 42% ROI in 3 yrs · tangible savings + brand trust + agility |
| **3** | Week 7 | Governance & Compliance | SOC 2, ISO 27001, HIPAA frameworks · IAM audits · AES-256 encryption |
| **4** | Week 8 | Governance Model Refinement | Monthly Cloud Steering Committee · 99.9% uptime SLA |
| **5** | Week 9 | Implementation Roadmap & DevOps | Automation >70% → +50% deploy frequency, −40% failure rate |
| **6** | Week 10 | Performance & Resilience Design | Non-linear cost curve · optimal redundancy = Level 4 (99.95% uptime) |
| **7** | Week 11 | Innovation & AI Integration | ROI plateaus after $300K investment · 80% data quality = max gain |
| **8** | Week 12 | Adoption Curve & Change Management | Logistic S-curve adoption · resistance decay · training + leadership drivers |
| **9** | Week 13 | Product Diffusion & Market Strategy | S-curve customer adoption · B2B/B2C · marketing spend + network effects |

### Extra Credit

| Panel | Topic | Key Result |
|-------|--------|------------|
| **EC 1** | Cloud Cost Management | AWS Cost Explorer demo · FinOps insights · MoM change · anomaly detection · cost-reduction simulation |
| **EC 2** | End-to-End Cloud Product Deployment | Amplify + Cognito · JWT session gate · deployment & cost documentation in-app |

---

## Tech Stack

- Pure HTML + JavaScript (no framework, no install)
- Chart.js for interactive charts
- Mermaid.js for diagrams (governance org chart + EC2 architecture)
- **Amazon Cognito** + **AWS Amplify JS** (CDN ES module) for sign-in (EC 2)
- Hosted on **AWS Amplify** (recommended for EC 2) or static hosting such as GitHub Pages (without auth)

---

## Shutting down AWS resources (cost control)

- Set **AWS Budgets** and **billing alarms** (e.g. notify at $5 forecasted).  
- Delete or disable the **Amplify app** when the term ends.  
- Delete the **Cognito user pool** if you no longer need identities.  
- In-panel notes under **EC 2 · Deployment & Ops** mirror these items for graders.
