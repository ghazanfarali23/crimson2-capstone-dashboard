# Extra Credit 2 — AWS Setup Guide (Do This First)

> **Goal:** Create a Cognito User Pool + launch an EC2 instance so your dashboard goes live.  
> **Time needed:** ~10–15 minutes  
> **Cost:** $0 if you're on AWS Free Tier; otherwise ~$8/month.

---

## PART 1 — Create the Cognito User Pool (5 min)

1. Go to [https://console.aws.amazon.com/cognito](https://console.aws.amazon.com/cognito) and sign in.
2. Click **"Create user pool"**.
3. **Provider types:** Select **Cognito** → click **Next**.
4. **Sign-in options:** Check **Email** only → click **Next**.
5. **Security requirements:** Leave defaults (minimum password length 8) → click **Next**.
6. **Multi-factor authentication:** Select **No MFA** → click **Next**.
7. **User account recovery:** Make sure **Email** is selected → click **Next**.
8. **Self-service sign-up:** Check **Enable self-registration** → click **Next**.
9. **Required attributes:** Check **Email** → click **Next**.
10. **Custom attributes:** Skip → click **Next**.
11. **Message delivery:** Leave defaults (Send email via Cognito) → click **Next**.
12. **Integrate your app:**
    - User pool name: `crimson2-pool`
    - **Initial app client:** Check **Yes**
    - App client name: `crimson2-client`
    - **Generate a client secret:** **UNCHECK THIS** (must be NO secret for a browser app)
    - Click **Next**
13. Review → click **Create user pool**.

**Copy these three values and paste them somewhere safe (Notes app):**
- `User pool ID` (looks like `us-east-1_XXXXXXXXX`)
- `Client ID` (a long alphanumeric string)
- `Region` (the part before the underscore, e.g. `us-east-1`)

---

## PART 2 — Fill in Cognito IDs (2 min)

### In the separate EC2 repo:

Open `ec2-deployment/index.html` and scroll to the very bottom. Find this block:

```javascript
const COGNITO_USER_POOL_ID        = 'us-east-1_XXXXXXXXX'; // ← YOUR POOL ID
const COGNITO_USER_POOL_CLIENT_ID = 'YOUR_CLIENT_ID_HERE'; // ← YOUR CLIENT ID
const COGNITO_REGION              = 'us-east-1';           // ← YOUR REGION
```

Replace the placeholders with your real values from Part 1.

Save, commit, and push to your separate GitHub repo.

---

## PART 3 — Launch the EC2 Instance (5 min)

1. Go to [https://console.aws.amazon.com/ec2](https://console.aws.amazon.com/ec2).
2. In the left menu, click **Instances** → **Launch instances**.
3. **Name:** `crimson2-dashboard`
4. **Application and OS Images:** Select **Amazon Linux 2023 AMI** (should be the first one in the list).
5. **Instance type:** Select **`t3.micro`** (Free tier eligible).
6. **Key pair:**
   - If you don't have one, click **Create new key pair**
   - Name it `crimson2-key`
   - Format: **.pem**
   - Click **Create key pair** (it downloads automatically — keep this file!)
7. **Network settings:**
   - Click **Edit**
   - VPC: leave default
   - **Create security group**
   - Security group name: `crimson2-sg`
   - **Inbound security group rules:**
     - **Rule 1:** Type = **SSH**, Source = **My IP** (dropdown)
     - **Rule 2:** Click **Add security group rule**
       - Type = **HTTP**, Source = **Anywhere** (`0.0.0.0/0`)
8. **Advanced details:**
   - Scroll down to **User data**
   - Select **As text**
   - Paste the contents of `ec2-deployment/user-data.sh` (from this repo)
   - **IMPORTANT:** Replace `https://github.com/YOUR_USERNAME/crimson2-ec2-deployment.git` with your actual GitHub repo URL
9. **Summary:** Click **Launch instance**.

Wait ~1–2 minutes for the instance to finish initializing.

---

## PART 4 — Assign an Elastic IP (2 min)

1. In the EC2 console left menu, click **Elastic IPs**.
2. Click **Allocate Elastic IP address**.
3. Click **Allocate**.
4. Select the new Elastic IP → click **Actions** → **Associate Elastic IP address**.
5. **Instance:** Select your `crimson2-dashboard` instance.
6. Click **Associate**.

**Copy the Elastic IP address** (e.g. `54.123.45.67`).

---

## PART 5 — Update Cognito with the Live URL (2 min)

1. Go back to [https://console.aws.amazon.com/cognito](https://console.aws.amazon.com/cognito).
2. Click your `crimson2-pool`.
3. Click the **App integration** tab.
4. Scroll down to **App client list** → click `crimson2-client`.
5. Click **Edit** (top right).
6. Scroll to **Hosted UI** section:
   - **Allowed callback URLs:** Add `http://YOUR_ELASTIC_IP`
   - **Allowed sign-out URLs:** Add `http://YOUR_ELASTIC_IP`
   - (If those fields don't exist, look for **App client settings** and add the URL there)
7. Click **Save changes**.

---

## PART 6 — Test It (1 min)

1. Open a browser and go to `http://YOUR_ELASTIC_IP`
2. You should see the **Cognito login screen**.
3. Click **Sign up** → enter a real email address → create a password.
4. Check your email for the confirmation code.
5. Enter the code → click **Sign in**.
6. The dashboard should appear! Try clicking **Sign out** to verify it works.

---

## PART 7 — Update Your Main Repo (2 min)

After everything works, update the placeholders in your **main** capstone repo:

1. `index.html` — EC2 panel: replace `YOUR_ELASTIC_IP`, `YOUR_AMPLIFY_DOMAIN`, and `YOUR_USERNAME`
2. `README.md` — replace the same placeholders
3. Commit and push.

---

## Quick AWS CLI Alternative (If You Prefer Terminal)

If you have the AWS CLI installed and configured, you can run these commands instead of clicking through the console:

```bash
# 1. Create Cognito User Pool
aws cognito-idp create-user-pool --pool-name crimson2-pool \
  --auto-verified-attributes email \
  --policies 'PasswordPolicy={MinimumLength=8,RequireUppercase=true,RequireLowercase=true,RequireNumbers=true,RequireSymbols=false}'

# Save the UserPool.Id from the output

# 2. Create App Client (no secret)
aws cognito-idp create-user-pool-client --user-pool-id YOUR_POOL_ID \
  --client-name crimson2-client \
  --no-generate-secret \
  --explicit-auth-flows ALLOW_USER_PASSWORD_AUTH ALLOW_USER_SRP_AUTH

# 3. Launch EC2 (replace key-name and user-data)
aws ec2 run-instances --image-id ami-xxxxxxxxxxxxxxxxx \
  --count 1 --instance-type t3.micro \
  --key-name crimson2-key \
  --security-groups crimson2-sg \
  --user-data file://user-data.sh

# 4. Allocate and associate Elastic IP
aws ec2 allocate-address --domain vpc
aws ec2 associate-address --instance-id i-xxxxxxxxxxxxxxxxx --allocation-id eipalloc-xxxxxxxx
```

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| "Cognito is not configured" message | You forgot to fill in the IDs in `index.html` |
| Nginx default page shows | The `user-data.sh` script hasn't finished yet; wait 2 minutes and refresh |
| 403 Forbidden | Run `sudo chmod 755 /usr/share/nginx/html` on the instance |
| Can't SSH | Make sure your security group allows SSH from **My IP** (not 0.0.0.0/0) |
| Email code not received | Check spam/junk; Cognito emails sometimes land there |
| Elastic IP changed after reboot | You didn't attach an Elastic IP; follow Part 4 |

---

## Shutdown Checklist (When Course Ends)

- [ ] Terminate the EC2 instance
- [ ] Release the Elastic IP
- [ ] Delete the Cognito User Pool
- [ ] Delete the security group (if not reused)
- [ ] Set AWS Budgets alarm at $5 (do this now, not later!)
