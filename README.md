## 🚀 React + Vite App Deployment to AWS S3 using GitHub Actions

This project demonstrates how to deploy a **React + Vite** application to an **AWS S3** bucket using a **CI/CD pipeline powered by GitHub Actions**. Once pushed to the `main` branch, the app is automatically built and deployed.

## 🧠 Prerequisites

Before you begin, ensure you have:

- ✅ AWS account with S3 access
- ✅ An S3 bucket created and configured for static website hosting
- ✅ IAM user with `AmazonS3FullAccess` (or custom limited policy)
- ✅ GitHub repository with your React + Vite code
- ✅ Basic knowledge of GitHub Actions

## 🪄 Step-by-Step Setup

### 1. 🛠 Create an IAM User in AWS

- Go to [AWS IAM Console](https://console.aws.amazon.com/iam/)
- Create a user with **Programmatic Access**
- Attach the following policy (minimum required):

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/y98m1ghbs5erbt7ch4tc.png)

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hj2r5ntnc6wf9q6c6rlf.png)

### ✅ 2. Create an S3 Bucket

- Go to the [AWS S3 Console](https://s3.console.aws.amazon.com)
- Click **Create bucket**
- Provide:
    - **Bucket name**: `your-unique-bucket-name`
    - **Region**: e.g., `Asia Pacific (Mumbai) - ap-south-1`
- Leave other settings as default and click **Create bucket**

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/urqf3aniusu7epluhl1m.png)

### 🌐 3. Enable Static Website Hosting

- Go to your created bucket
- Click on the **Properties** tab
- Scroll to **Static website hosting** > Click **Edit**
- Enable `Static website hosting`
- Set:
   - **Index document**: `index.html`
   - **Error document**: `index.html` *(or `error.html` optionally)*
- Click **Save changes**

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e1fyiz6d1ro64scg01wl.png)

### 🔓 4. Allow Public Access

- Go to the **Permissions** tab
- Under **Block public access (bucket settings)**, click **Edit**
- **Uncheck all** options (especially "Block all public access")
- Confirm by checking the warning box
- Click **Save changes**

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3ha5aood0vq44zwyjkmm.png)

### 👥 5. Set Object Ownership to Public

- Still under the **Permissions** tab
- Scroll to **Object Ownership** and click **Edit**
- Choose:
   - `ACLs enabled`
   - `Bucket owner preferred`
- Save the changes


![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lcvrslrkitemnriyl7he.png)

### 🛡 6. Attach Public Read Policy Using Policy Generator

#### 🧰 Use AWS Policy Generator:

- Open: [AWS Policy Generator](https://awspolicygen.s3.amazonaws.com/policygen.html)

**Fill in:**
- **Effect**: `Allow`
- **Principal**: paste-your-IAM-user-ARN
- **Action**: `s3:GetObject`
- **ARN**:  paste-your-S3-Bucket-ARN

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/2m2by1x1icda1zpkodj0.png)

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gwaym21p2tlf2y891r3o.png)

### 7. Create access-keys for IAM user

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/gqgpp46qnzq00v4w305z.png)

### 8. Go to GitHub Actions

- Open your GitHub repository.
- Click on the **"Actions"** tab.
- Click on **"Set up a workflow yourself"** or choose **"New workflow"**.


### 9. Create a Workflow File

- In the `.github/workflows/` folder, create a file named:  main.yml
- paste below code in the code area and click commit changes.

```
name: Upload Website

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Install dependencies
        run: npm ci

      - name: Build Vite app
        run: npm run build

      - name: Sync dist folder to S3
        uses: jakejarvis/s3-sync-action@master
        with:
          args: --acl public-read --follow-symlinks --delete
        env:
          AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_REGION: 'eu-north-1'
          SOURCE_DIR: 'dist' 
```
 
### 10. Add New repository secrets

- Go to settings > secrets and variables
- click add new repository secret.
- click actions.

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0ig5oa6cc162z13w6a92.png)

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/xrwgr9d7sfnye46o2w7h.png)

![omkarsharma2821](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/b6dgc3myzqixw9fbwwe9.png)