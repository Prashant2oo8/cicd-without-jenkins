# Automated AWS CI/CD Pipeline for Static Website Hosting

This repository contains the source code and configuration for an automated Continuous Integration and Continuous Deployment (CI/CD) pipeline built using AWS native services and GitHub.

The pipeline automatically builds and deploys static website assets (`HTML`, `CSS`, `JavaScript`) to an Amazon S3 bucket configured for static website hosting whenever changes are pushed to the `main` branch.

---

## 🏗️ Architecture Overview

```text
[ GitHub Repo ] 
      │ (Push to main branch)
      ▼
[ AWS CodePipeline ]
      │
      ├──> 1. Source Stage (Retrieves latest commit)
      │
      ├──> 2. Build Stage (AWS CodeBuild executes buildspec.yml)
      │
      └──> 3. Deploy Stage (Syncs assets to Target S3 Bucket)
               │
               ▼
   [ Amazon S3 Bucket ] ──> Public Static Website Endpoint
```

---

## 🛠️ Tech Stack & Services

* **Version Control:** GitHub
* **CI/CD Orchestration:** AWS CodePipeline
* **Build Engine:** AWS CodeBuild
* **Storage & Hosting:** Amazon S3 (Static Website Hosting)
* **Access Control:** IAM Roles & Policies

---

## 📁 Repository Structure

```text
.
├── buildspec.yml               # CodeBuild configuration file
├── templatemo_630_helix_drift/ # Web assets folder (HTML, CSS, JS, images)
│   ├── index.html
│   ├── css/
│   └── js/
└── README.md                   # Project documentation
```

---

## ⚙️ Build Specification (`buildspec.yml`)

The `buildspec.yml` file uses `base-directory` to extract files directly from the subfolder into the root level of the target S3 deployment bucket:

```yaml
version: 0.2

phases:
  build:
    commands:
      - echo "Packaging static website files..."
artifacts:
  base-directory: 'templatemo_630_helix_drift'
  files:
    - '**/*'
```

---

## 🚀 Setup & Deployment Instructions

### 1. Amazon S3 Bucket Setup
1. Create a general-purpose S3 bucket (e.g., `prsnt-cicd-practical`).
2. Enable **Static Website Hosting** under the **Properties** tab.
3. Set `index.html` as the Index document.
4. Configure bucket public access / CloudFront permissions according to your deployment strategy.

### 2. AWS CodePipeline & CodeBuild Setup
1. Create a new pipeline in **AWS CodePipeline**.
2. Connect your **GitHub** repository and select the `main` branch as the trigger.
3. In the **Build stage**, create an **AWS CodeBuild** project:
   * **Environment:** Managed Image (Ubuntu / Standard).
   * **Build specifications:** Select *Use a buildspec file*.
4. In the **Deploy stage**, choose **Amazon S3** as the provider and select your target bucket. Ensure **Extract file before deploy** is checked.

---

## 🔄 How to Trigger Deployment

To make updates to your website, simply push your changes to the `main` branch:

```bash
git add .
git commit -m "Update website content"
git push origin main
```

AWS CodePipeline will automatically detect the commit, run the build via CodeBuild, and update your live site in S3.
