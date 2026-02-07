# Deploying a React Static Website on AWS S3 + CloudFront (Production)

This repository provides a **step-by-step guide** to deploy a **React static website** on **AWS S3 (private bucket)** and serve it securely using **AWS CloudFront**, while ensuring proper support for React routing.

This method is suitable for **production deployments** of React applications.

---

## ✅ Prerequisites

Before you begin, make sure you have:

* ✅ Node.js & npm installed
  Check using:

  ```bash
  node -v
  npm -v
  ```
* ✅ An AWS account
  Sign up at: [https://aws.amazon.com/](https://aws.amazon.com/)
* Basic understanding of React and AWS Console

---

# 🛠 Step 1 — Setup React Project

If you already have a React project, you can skip this step.

Otherwise, create a new React app:

```bash
npx create-react-app my-react-app
cd my-react-app
```

Install dependencies:

```bash
npm install
```

Run locally to verify everything works:

```bash
npm start
```

---

# 🏗 Step 2 — Build React App for Production

Generate optimized production build:

```bash
npm run build
```

After this command completes, a **`build/`** folder will be created inside your project.

📂 **This `build/` folder contains your production-ready static website.**

---

# ☁️ Step 3 — Create an S3 Bucket (AWS Console)

1. Go to **AWS Console → S3**
2. Click **Create bucket**
3. Enter bucket name (example: `my-react-prod-site`)
4. Choose a region
5. **Uncheck** “Block all public access”
6. Click **Create bucket**

> ⚠️ Note: The bucket will still remain private; only CloudFront will access it.

---

# 📤 Step 4 — Upload Build Files to S3

1. Open your newly created S3 bucket
2. Click **Upload**
3. Select all files inside your local **build/** folder
4. Click **Upload**

---

# 🔒 Step 5 — Make S3 Bucket Accessible Only via CloudFront

Go to:

**S3 → Your Bucket → Permissions → Bucket Policy**

Add the following policy (replace placeholders accordingly):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCloudFrontAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity CLOUDFRONT_OAI_ID"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
    }
  ]
}
```

Click **Save changes**

---

# 🌍 Step 6 — Enable Static Website Hosting in S3

Go to:

**S3 → Your Bucket → Properties → Static website hosting**

* Enable static website hosting
* Index document: `index.html`
* Error document: `index.html` (important for React routing)

Click **Save**

---

# 🌐 Step 7 — Create CloudFront Distribution

Go to: **AWS Console → CloudFront → Create Distribution**

### Origin Settings:

* Origin Domain: Select your S3 bucket
* Origin Access: Choose **Origin Access Control (OAC)**
* Create a new OAC

### Viewer Protocol Policy:

* Select **Redirect HTTP to HTTPS**

### Default Root Object:

```
index.html
```

### Custom Error Responses (Important for React Routing)

Add the following rules:

| Error Code | Response Page | Response Code |
| ---------- | ------------- | ------------- |
| 403        | /index.html   | 200           |
| 404        | /index.html   | 200           |

Click **Create Distribution**

⏳ Wait 5–10 minutes for CloudFront to deploy.

---

# 🔁 Step 8 — Clear CloudFront Cache After Updates

Whenever you deploy new changes:

1. Go to **CloudFront → Your Distribution**
2. Click **Invalidations**
3. Enter:

```
/*
```

4. Click **Create**

This ensures users see the latest version of your site.

---

# 🌍 Step 9 — Access Your Website

After CloudFront is deployed, you will get a URL like:

```
https://d123abcxyz.cloudfront.net
```

Use this as your production website URL.

(Optional) You can later attach your own custom domain using Route 53.

---

# 🎯 Final Outcome

By following this guide, you now have:

✔ A React static site
✔ Hosted on a **private S3 bucket**
✔ Served securely via **CloudFront**
✔ Proper React routing with index.html fallback
✔ Production-ready deployment

---

# 🤖 (Optional) Automate with GitHub Actions

If you want, I can provide:

* GitHub Actions workflow to:

  * Run `npm install`
  * Run `npm run build`
  * Automatically upload to S3

## Just open an issue or ask in discussions 🚀

Happy Deploying! 🎉
