# **Azure DevOps Pipeline Setup & Best Practices**
This repository contains an **Azure DevOps pipeline** for building, testing, and deploying a `.NET` application across **Development**, **Staging**, and **Production** environments with advanced features like:
- **Blue-Green Deployment**
- **Rollback Automation**
- **Testing & Security Scanning**
- **Notifications & Alerts**
- **Retry Logic**
- **GitHub Sharing & Documentation**

---

## **1. Pipeline Configuration**
### **Main Pipeline (`azure-pipeline.yml`)**
This file defines the full **CI/CD workflow** for deployment.

```yaml
stages:
  - stage: Testing
    jobs:
      - template: test-template.yml

  - stage: Security_Scanning
    jobs:
      - template: security-template.yml

  - stage: Deploy_Prod
    condition: eq(variables['Build.SourceBranchName'], 'main')
    jobs:
      - job: Approval
        pool: server
        steps:
          - task: ManualValidation@0
            inputs:
              instructions: 'Confirm deployment to production'
              onTimeout: 'reject'

      - job: Deploy_Prod
        dependsOn: Approval
        jobs:
          - template: deploy-template.yml
            parameters:
              environment: 'Production'
              appName: 'myapp-prod'
              slotName: 'blue'
