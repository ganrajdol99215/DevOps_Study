# 🚀 DevOps, Cloud, Docker, Kubernetes Interview Q&A

This document contains **all questions and answers**, formatted for easy reference.

## 🎯 Round 2: Technical Round 

### 1. You’ve got an app running in one AWS account that needs to access an S3 bucket in another account. How would you set that up securely?

**Answer:**
- Use an S3 bucket policy granting access to the IAM role in the other account.
- Example:
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
- Optionally configure `sts:AssumeRole`.

### 2. Can you write a Dockerfile for a Node.js app using multi-stage builds — just something you’d actually use in a real project?

**Answer:**
```dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --only=production
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### 3. Suppose your Terraform state file gets corrupted or out of sync. What steps would you take to recover from that?

**Answer:**
- Restore from S3 versioning backup.
- Run `terraform state pull` to review.
- Remove invalid resources with `terraform state rm`.
- Re-import resources with `terraform import`.
- Plan/apply carefully.

### 4. You have an EC2 in a private subnet and can’t use a NAT Gateway. How else can it reach the internet for updates or downloads?

**Answer:**
- Use a NAT instance.
- Use VPC endpoints.
- Temporarily attach a public IP.
- Use Systems Manager Session Manager.

### 5. A container has crashed or exited suddenly — how would you go about figuring out what went wrong?

**Answer:**
- `docker logs <container>`
- `docker inspect` for exit codes.
- `kubectl describe pod` and `kubectl logs` in Kubernetes.
- Check resource limits.

### 6. There’s an existing AWS VPC created manually, but now you want to manage it through Terraform. How would you import it and make sure nothing breaks?

**Answer:**
- Create a matching resource block.
- `terraform import aws_vpc.main vpc-xxxx`
- Use `terraform state show`.
- Adjust `.tf` to match actual state.
- `terraform plan` to validate.

### 7. How would you roll out blue-green deployments in a Kubernetes cluster? What would that look like in practice?

**Answer:**
- Deploy new Deployment ("green").
- Test the green pods.
- Update Service selector to point to green.
- Scale down blue Deployment.
- Automate with ArgoCD.

### 8. When using Terraform, how do you manage sensitive values like secrets or API keys without hardcoding them?

**Answer:**
- Environment variables (`TF_VAR_secret`).
- `.tfvars` files in `.gitignore`.
- Vault or AWS SSM.
- `sensitive = true` outputs.

### 9. In a Dockerfile, what’s the practical difference between using COPY and ADD? When would you prefer one over the other?

**Answer:**
- `COPY` only copies files.
- `ADD` extracts tars or fetches URLs.
- Prefer `COPY` unless `ADD` features needed.

### 10. If you had to create resources across two different AWS accounts using Terraform, how would you set that up?

**Answer:**
- Configure multiple providers with aliases.

```hcl
provider "aws" {
  alias  = "account1"
  region = "us-east-1"
  profile = "account1"
}

provider "aws" {
  alias  = "account2"
  region = "us-east-1"
  profile = "account2"
}

resource "aws_s3_bucket" "a" {
  provider = aws.account1
  bucket   = "account1-bucket"
}

resource "aws_s3_bucket" "b" {
  provider = aws.account2
  bucket   = "account2-bucket"
}
```

### 11. Imagine you have a PHP app in a Docker container that needs MySQL credentials — how would you pass those securely?

**Answer:**
- Use environment variables or secrets.
- In Compose:

```yaml
environment:
  MYSQL_PASSWORD: ${MYSQL_PASSWORD}
```

- Kubernetes Secrets.

### 12. If someone manually changed an S3 bucket policy that was originally created with Terraform, how would you deal with that kind of drift?

**Answer:**
- `terraform plan` to detect drift.
- Re-apply to restore.
- Educate team on IaC practices.
- Use stricter IAM controls.

### 13. How do you enforce rules in Kubernetes to control which pods can talk to each other?

**Answer:**
- Use NetworkPolicies.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
    - Ingress
```

### 14. Could you write a small Python script that backs up all files older than 30 days from a folder?

**Answer:**
```python
import os
import shutil
import time

src = "/path/to/source"
dst = "/path/to/backup"
now = time.time()

for f in os.listdir(src):
    fp = os.path.join(src, f)
    if os.path.isfile(fp) and (now - os.path.getmtime(fp)) > 30 * 86400:
        shutil.copy2(fp, dst)
```

### 15. If your team is seeing a spike in cloud costs, how would you go about figuring out why — and cutting cost without hurting performance?

**Answer:**
- AWS Cost Explorer.
- Check idle resources.
- Autoscaling and rightsizing.
- Use reserved/spot instances.

### 16. Say you want to serve users in different countries using AWS. How would you route traffic based on user location?

**Answer:**
- Route 53 Geolocation routing.
- CloudFront distributions.

### 17. You’re on-call. A production Kubernetes cluster is a mess — pods aren’t pulling images, some are getting evicted, and users are seeing errors. How do you troubleshoot this, and how would you prevent it next time?

**Answer:**
- `kubectl get events`
- `kubectl describe node`
- Check disk/memory pressure.
- Fix imagePullSecrets.
- Add monitoring and quotas.

## 🌿 Round 3: Behavioral Round

### 1. What would you do if you were asked to work on a tool or technology you’ve never used before?

**Answer:**
- Research documentation.
- Build a proof of concept.
- Ask colleagues.
- Document findings.

### 2. Can you share a time when you had to deliver something quickly, but didn’t have all the time or resources you needed? How did you manage it?

**Answer:**
- Prioritize essentials.
- Communicate constraints.
- Deliver MVP.
- Iterate improvements.

### 3. Tell me about a mistake or outage you were involved in — how did you respond, and what did you learn from it?

**Answer:**
- Rolled back.
- Added validations.
- Shared lessons learned.

### 4. What’s the hardest technical issue you’ve faced so far? How did you go about solving it?

**Answer:**
- Debugged node failures.
- Logs and metrics.
- Refined scaling.

### 5. If you wanted to convince your team or manager to adopt a new tool or process, how would you make your case?

**Answer:**
- Build a POC.
- Present pros/cons.
- Show metrics.

### 6. Can you talk about a time when you had to pick up a new tool or skill quickly to solve a real problem?

**Answer:**
- Learned Terraform.
- Built prototypes quickly.
- Delivered solution.

✅ **End of Document**
