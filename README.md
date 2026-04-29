# Crimson 2 — Harvard Cloud Management Capstone Dashboard

**Group:** Crimson 2  ·  Ghazanfar · Aaron · Chloe  
**Course:** Advanced Cloud Management — Harvard Extension School, Spring 2026  
**Project:** MedData AWS Migration  

**Live (demo / fork-specific):**  

- Historical static host: [GitHub Pages](https://ghazanfarali23.github.io/crimson2-capstone-dashboard/) (no Cognito gate on older snapshots).  
- **Primary deployment (Amplify):** `https://YOUR_AMPLIFY_DOMAIN.amplifyapp.com` ← update after connecting to Amplify  
- **EC2 deployment (Extra Credit 2):** `http://YOUR_ELASTIC_IP` ← update after launching EC2  
- **EC2 deployment repo:** `https://github.com/YOUR_USERNAME/crimson2-ec2-deployment` ← update after creating separate repo

---

## About

This dashboard is our **capstone deliverable**, combining all 9 weekly panels plus extra credit into a single interactive web app. Each panel corresponds to a week of the capstone and addresses a different dimension of the MedData cloud migration to AWS.

---

## Authentication (Extra Credit 2 — AWS Cognito)

The SPA uses **AWS Amplify JavaScript v6** loaded from [esm.sh](https://esm.sh) inside a `type="module"` script at the bottom of `index.html`. Only **public** identifiers belong in the repo:


| Constant                      | Where to find it                                                                                                                  |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `COGNITO_USER_POOL_ID`        | Cognito → *User pools* → your pool → **User pool ID** (e.g. `us-east-1_AbCdEfGh`)                                                 |
| `COGNITO_USER_POOL_CLIENT_ID` | Same pool → *App integration* → **App clients** → **Client ID** (use an app client **without** a client secret for a browser SPA) |
| `COGNITO_REGION`              | The region that matches the prefix of the pool ID (e.g. `us-east-1`)                                                              |


**Do not** commit IAM user keys, machine credentials, or an app client **secret**. For a hosted single-page app, create a Cognito **hosted UI–style** or standard app client configured for **USER_PASSWORD_AUTH** / **ALLOW_USER_SRP_AUTH** as required by your assignment, and allow self sign-up if you use the built-in sign-up form.

When the constants are left empty, visitors see a **configuration required** message and cannot reach `#app`.

After editing `index.html`, push to the branch connected to **Amplify Hosting** so the live site picks up the change.

---

## Deployment Options

### Option 1 — AWS Amplify Hosting (Primary)

This repository is a **pure static** site: one `index.html`, no bundler. Amplify can deploy it with the included `**amplify.yml`**:

- `build` runs a no-op `echo` (Amplify expects a build phase).
- `artifacts.baseDirectory` is `**.`** so the root `index.html` is published.

Behind Amplify, AWS provisions **S3** for objects and **CloudFront** for HTTPS and edge caching—you do not manage those buckets separately for a standard Amplify web app.

### Option 2 — Amazon EC2 (Extra Credit 2)

A separate repository (`ec2-deployment/`) contains the files needed to deploy this dashboard on a single **EC2** instance running **Amazon Linux 2023 + nginx**:

- `index.html` — the dashboard with Cognito constants ready for your pool IDs.
- `user-data.sh` — bootstrap script that installs nginx and clones the repo on first launch.
- `README.md` — full deployment instructions, cost estimates, security notes, and shutdown steps.

**Quick start:**
1. Create a new GitHub repo and push the contents of `ec2-deployment/`.
2. Fill in your Cognito IDs in `index.html`.
3. Launch a `t3.micro` EC2 instance and paste `user-data.sh` into **Advanced → User data**.
4. Attach an **Elastic IP** for a stable URL.
5. Add the Elastic IP to your Cognito App Client’s allowed callback / sign-out URLs.
6. Update the links in this README and in the dashboard’s EC2 panel.

---

## The 9 Panels


| Panel | Week    | Topic                               | Key Result                                                                   |
| ----- | ------- | ----------------------------------- | ---------------------------------------------------------------------------- |
| **1** | Week 5  | Budget Planning & Cost Estimation   | CapEx eliminated: $320K/yr servers · OpEx: $18K/mo AWS · ROI horizon: 3 yrs  |
| **2** | Week 6  | ROI & Value Analysis                | 42% ROI in 3 yrs · tangible savings + brand trust + agility                  |
| **3** | Week 7  | Governance & Compliance             | SOC 2, ISO 27001, HIPAA frameworks · IAM audits · AES-256 encryption         |
| **4** | Week 8  | Governance Model Refinement         | Monthly Cloud Steering Committee · 99.9% uptime SLA                          |
| **5** | Week 9  | Implementation Roadmap & DevOps     | Automation >70% → +50% deploy frequency, −40% failure rate                   |
| **6** | Week 10 | Performance & Resilience Design     | Non-linear cost curve · optimal redundancy = Level 4 (99.95% uptime)         |
| **7** | Week 11 | Innovation & AI Integration         | ROI plateaus after $300K investment · 80% data quality = max gain            |
| **8** | Week 12 | Adoption Curve & Change Management  | Logistic S-curve adoption · resistance decay · training + leadership drivers |
| **9** | Week 13 | Product Diffusion & Market Strategy | S-curve customer adoption · B2B/B2C · marketing spend + network effects      |


### Extra Credit


| Panel    | Topic                               | Key Result                                                                                            |
| -------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------- |
| **EC 1** | Cloud Cost Management               | AWS Cost Explorer demo · FinOps insights · MoM change · anomaly detection · cost-reduction simulation |
| **EC 2** | End-to-End Cloud Product Deployment | Amplify + Cognito · JWT session gate · deployment & cost documentation in-app                         |


---

## Tech Stack

- Pure HTML + JavaScript (no framework, no install)
- Chart.js for interactive charts
- Mermaid.js for diagrams (governance org chart + EC2 architecture)
- **Amazon Cognito** + **AWS Amplify JS** (CDN ES module) for sign-in (EC 2)
- Hosted on **AWS Amplify** (primary) or **Amazon EC2 + nginx** (Extra Credit 2) or static hosting such as GitHub Pages (without auth)

---

## Shutting down AWS resources (cost control)

- Set **AWS Budgets** and **billing alarms** (e.g. notify at $5 forecasted).  
- Delete or disable the **Amplify app** when the term ends.  
- **Stop or terminate** the **EC2 instance** and **release the Elastic IP** when the course ends.  
- Delete the **Cognito user pool** if you no longer need identities.  
- In-panel notes under **EC 2 · Deployment & Ops** mirror these items for graders.