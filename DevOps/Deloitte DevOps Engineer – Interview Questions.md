
# Advanced DevOps & Cloud Infrastructure Interview Questions with Detailed Answers

## 1. Caching Docker Layers or NuGet/NPM Packages in Pipelines
**Techniques:**
- **Docker**: Use multistage builds and ensure layers with fewer changes come first. Cache Docker layers using self-hosted runners or Docker cache actions.
- **NPM/Yarn**: Use `cache` directive in GitHub Actions or Azure DevOps to store `.npm` or `.yarn` directories.
- **NuGet**: Cache `~/.nuget/packages` or use Azure Artifacts feeds.

## 2. Secure, Auditable CI/CD in Azure DevOps
**Architecture:**
- Separate pipelines for Dev/Test/Prod.
- Use Azure Key Vault and RBAC.
- Enable audit logs, use approvals, manual gates.
- Store templates in separate repo for reuse.

## 3. Azure Key Vault Integration with Pipelines
**Steps:**
- Use `AzureKeyVault@2` task in YAML.
- Enable managed identity or service connection with RBAC.
- Configure secret rotation via Key Vault Events and Azure Automation.

## 4. Rollback Strategies
**Strategies:**
- Deployment slots (App Services)
- Helm rollback (AKS)
- Terraform state rollback (via backups)
- Example: Canary deployment failed in Prod; used Helm rollback and disabled traffic via Istio.

## 5. Manual Intervention Gates
**Use Cases:**
- Approval before production deployment.
- Use `ManualValidation@0` task in Azure DevOps.
- Add branch policies and stage approvals.

## 6. Compliance as Code in Azure DevOps
**Includes:**
- Code scanning (SonarQube, Checkmarx)
- Security linting (Trivy, ESLint)
- Code coverage with tools like `coverlet`, `nyc`
- Policy checks (OPA, Azure Policy via tasks)

## 7. Pipeline Security
**Practices:**
- Enforce branch protection in Git.
- Scan dependencies (Dependabot, Snyk).
- Use environment secrets, not hardcoded.
- Sign commits and enable MFA.

## 8. Shared Libraries/Templates
- Jenkins: `vars/` and `src/` directories.
- Azure DevOps: `template.yml` reused via `extends`.
- DRY pipelines in large orgs.

## 9. Terraform State Management in Teams
**Strategies:**
- Use remote backend (Azure Blob with locking)
- Enable state locking via CosmosDB
- Enable versioning for backup
- Restrict write access

## 10. Multi-Repo CI/CD for Microservices
**Strategies:**
- Use GitHub/Azure DevOps matrix builds or triggers.
- Use artifact-based coordination.
- Use orchestrator repo for deployment.

## 11. Modular Terraform
**Example Structure:**
```
/modules/network
/modules/compute
/envs/dev/main.tf
```
- Use `locals`, `outputs`, `backend` in `main.tf`

## 12. Classic vs YAML Pipelines
**Classic:**
- GUI-based, less portable.
**YAML:**
- Code versioned, reusable, better for IaC.

## 13. Lifecycle Blocks in Terraform
**Use Cases:**
- `prevent_destroy`: Prevent accidental deletes.
- `create_before_destroy`: Zero downtime infra change.

## 14. Drift Detection in Terraform
**Tools:**
- `terraform plan`
- `terraform refresh`
- Use `infracost`, `tfsec`, and `driftctl`

## 15. Liveness vs Readiness Probes (K8s)
**Liveness**: App alive.
**Readiness**: App ready for traffic.
**Example:** Wrong path in readiness probe = traffic sent too early = errors.

## 16. Terraform Command Differences
- `plan`: Preview changes
- `refresh`: Sync state file with real infra
- `apply`: Apply changes

## 17. Remote Terraform Backend in Azure
**Steps:**
- Use Azure Storage Account + container for backend.
- Configure `backend "azurerm"`.
- Use CosmosDB for state locking.

## 18. Terraform `count` vs `for_each`
**Use-case:**
- `count`: For identical resources.
- `for_each`: For unique names/keys.

**Dynamic Blocks**: Use for repeated nested blocks (e.g. tags, rules).

## 19. Secure Multistage Dockerfile
```Dockerfile
# Stage 1
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./
RUN addgroup -S app && adduser -S app -G app
USER app
CMD ["node", "index.js"]
```

## 20. AKS Control Plane Components
- **kubelet**: Runs on nodes, manages containers.
- **kube-apiserver**: Front door to control plane.
- **kube-proxy**: Handles networking (Services → Pods).

## 21. Benefits of Multistage Docker Builds
- Smaller final image size.
- Exclude dev/build tools.
- Better cache and performance.

## 22. Docker Volumes vs Bind Mounts
- **Volume**: Docker-managed, persistent.
- **Bind Mount**: Host directory used. Good for dev.
- In K8s: Use volumes via PV/PVC.

## 23. Managing Secrets in AKS
- Use Azure Key Vault + CSI driver.
- Mount secrets to pods without exposing via env vars.
- Rotate via automation.

## 24. StatefulSet vs Deployment
- **StatefulSet**: Unique ID per pod, stable storage (DBs).
- **Deployment**: Stateless apps.

## 25. Blue-Green and Canary in AKS
**Tools:**
- **Helm**: Use `helm upgrade --set image.tag` and hooks.
- **Istio/Linkerd**: Traffic shifting via VirtualServices.

---

Let me know if you'd like these answers in PDF or DOCX format too.
