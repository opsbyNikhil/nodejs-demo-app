# 🚀 GitHub Actions CI/CD Pipeline

This project uses **GitHub Actions** to automate the build, test, code quality scan, Docker image creation, and deployment to **Amazon ECR (Elastic Container Registry)**.

---

# 📌 Workflow Name

```yaml
name: Github-Actions
```

This defines the pipeline name shown in the GitHub Actions tab.

---

# ⚡ Trigger Event

```yaml
on:
  push:
    branches:
      - main
```

### 🔁 When does this pipeline run?
- Every push to the **main branch**

---

# 🧱 Job Definition

```yaml
jobs:
  build:
    runs-on: self-hosted
```

### 🖥️ Execution Environment:
- Runs on a **self-hosted runner (EC2 / VM)**
- Not GitHub-hosted runners

---

# 🔄 Pipeline Steps

---

## 1️⃣ Checkout Code

```yaml
- name: checkout
  uses: actions/checkout@v6
```

### 📌 Purpose:
- Pulls source code from repository into runner

---

## 2️⃣ Setup Node.js

```yaml
- name: Setup NodeJS
  uses: actions/setup-node@v4
  with:
    node-version: 22
```

### 📌 Purpose:
- Installs Node.js v22 environment

---

## 3️⃣ Verify Node Version

```yaml
- name: Verify Node Version
  run: node -v
```

### 📌 Purpose:
- Confirms Node.js installation

---

## 4️⃣ Install Dependencies

```yaml
- name: Install Dependencies
  run: npm install
```

### 📌 Purpose:
- Installs all required packages from package.json

---

## 5️⃣ Build Application

```yaml
- name: Build Application
  run: npm run build
```

### 📌 Purpose:
- Builds production-ready application

---

## 6️⃣ SonarQube Code Scan

```yaml
- name: Sonar Cloud Scan
  uses: SonarSource/sonarqube-scan-action@v6
  env:
    SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

### 📌 Purpose:
- Performs code quality analysis
- Detects bugs, vulnerabilities, code smells

---

## 7️⃣ Configure AWS Credentials

```yaml
- name: Configure AWS Credentails
  uses: aws-actions/configure-aws-credentials@v1
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_KEY_ID }}
    aws-region: ${{ secrets.AWS_REGION }}
```

### 📌 Purpose:
- Authenticates AWS CLI inside runner

---

## 8️⃣ Login to Amazon ECR

```yaml
- name: Login to Amazon ECR
  uses: aws-actions/amazon-ecr-login@v2
```

### 📌 Purpose:
- Authenticates Docker with Amazon ECR

---

## 9️⃣ Build Docker Image

```yaml
- name: Docker image build
  run: docker image build -t devops-cmd-app:v1.0 .
```

### 📌 Purpose:
- Creates Docker image from Dockerfile

---

## 🔟 List Docker Images

```yaml
- name: Docker Images
  run: docker image ls
```

### 📌 Purpose:
- Shows available Docker images (debug step)

---

## 1️⃣1️⃣ Tag Docker Image

```yaml
- name: Tag Docker Image
  run: |
    docker tag devops-cmd-app:v1.0 ${{ secrets.ECR_REPOSITORY }}:latest
```

### 📌 Purpose:
- Tags image for Amazon ECR repository

---

## 1️⃣2️⃣ Push Docker Image to ECR

```yaml
- name: Docker Image Push to Amazon ECR
  run: |
    docker push ${{ secrets.ECR_REPOSITORY }}:latest
```

### 📌 Purpose:
- Uploads Docker image to AWS ECR

---

# 🧾 Secrets Used

| Secret Name | Purpose |
|-------------|--------|
| SONAR_TOKEN | SonarQube authentication |
| AWS_ACCESS_KEY_ID | AWS access |
| AWS_SECRET_KEY_ID | AWS secret |
| AWS_REGION | AWS region |
| ECR_REPOSITORY | ECR image repository |

---

# 🔥 Pipeline Flow

```
Code Push (main)
   ↓
Checkout Code
   ↓
Setup Node.js
   ↓
Install Dependencies
   ↓
Build Application
   ↓
SonarQube Scan
   ↓
Configure AWS Credentials
   ↓
Login to Amazon ECR
   ↓
Build Docker Image
   ↓
Tag Image
   ↓
Push to Amazon ECR
```

---

# 🚀 Final Output

✔ Code is built  
✔ Code quality checked  
✔ Docker image created  
✔ Image pushed to Amazon ECR  
✔ Ready for deployment (ECS / EKS / EC2)

---

# 🔥 Suggested Improvements

- Add test stage (`npm test`)
- Add SNS/Email notifications
- Use GitHub OIDC instead of AWS keys
- Use dynamic Docker tag (`${{ github.sha }}`)
- Add deployment stage (ECS/EKS/EC2)

---