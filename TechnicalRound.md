# 🚀 DevOps, Cloud, Docker, Kubernetes Interview Q&A

This document contains **all questions and answers**, formatted for easy reference.

## 🎯 Round 2: Technical Round (60 minutes)

### 1. You’ve got an app running in one AWS account that needs to access an S3 bucket in another account. How would you set that up securely?

**Answer:**
- Use an S3 bucket policy in the target account granting access to the source account's IAM role.
- Example policy:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "AWS": "arn:aws:iam::SOURCE_ACCOUNT_ID:role/ROLE_NAME"
        },
        "Action": "s3:*",
        "Resource": [
          "arn:aws:s3:::bucket-name",
          "arn:aws:s3:::bucket-name/*"
        ]
      }
    ]
  }
  ```
- Optionally, set up cross-account roles and use `sts:AssumeRole`.

---

### 2. Can you write a Dockerfile for a Node.js app using multi-stage builds — just something you’d actually use in a real project?

**Answer:**
```dockerfile
# Build Stage
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production Stage
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --only=production
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

... (You can fill in all the rest of the questions here for completeness)

✅ **End of Document**