# 🔄 CI/CD Pipeline - Neofile Document Management System

## 📋 Table of Contents
1. [Overview](#overview)
2. [Pipeline Architecture](#pipeline-architecture)
3. [Development Workflow](#development-workflow)
4. [Environment Strategy](#environment-strategy)
5. [Pipeline Stages](#pipeline-stages)
6. [Quality Gates](#quality-gates)
7. [Deployment Strategy](#deployment-strategy)
8. [Monitoring and Observability](#monitoring-and-observability)

---

## 🎯 Overview

This document describes the Continuous Integration and Continuous Delivery (CI/CD) strategy for Neofile. The objective is to guarantee high code quality, automated tests, and reliable deployments for microservices.

### Pipeline Goals
- Automated quality checks (tests, lint, coverage)
- Reliable deployment across development, QA, and production
- Rollback support
- Git-based workflow using Azure DevOps
- Microservice containerization and delivery to AWS ECS

---

## 🏗️ Pipeline Architecture

```
[Git Repo (Azure DevOps)]
       │
   [Pipelines]
       │
┌────────────┐
│  Build     │ - Compile, lint, test
└────────────┘
       │
┌────────────┐
│  Package   │ - Docker build + tag
└────────────┘
       │
┌────────────┐
│  Deploy    │ - Deploy to ECS via task definition
└────────────┘
```

Technologies:
- Version control: Git (Azure Repos)
- CI/CD: Azure Pipelines
- Container Registry: AWS ECR
- Deployment: AWS ECS (Fargate)
- Monitoring: CloudWatch

---

## 🔄 Development Workflow

### Git Strategy
- Branches: `main`, `develop`, `feature/*`, `hotfix/*`
- Pull Requests: required into `develop` and `main`

### Commit Format
```
<type>(<scope>): <message>
```
Example:
```
feat(documents): add GPT classification endpoint
```

---

## 🌍 Environment Strategy

| Environment    | Purpose                | Branch     | URL Example              |
|----------------|------------------------|------------|--------------------------|
| Development    | Dev testing            | `develop`  | `dev.neofile.com`        |
| QA             | Testing & validation   | `release/*`| `qa.neofile.com`         |
| Production     | Real user traffic      | `main`     | `app.neofile.com`        |

- Config via `application-dev.yml`, `application-prod.yml`

---

## 🔄 Pipeline Stages

### 1. Build
- Compile code
- Run unit tests
- Lint validation

### 2. Test
- Integration tests (per service)
- Contract tests (if needed)

### 3. Package
- Build Docker image
- Push to ECR with tags: `latest`, `commit_sha`

### 4. Deploy
- ECS deployment (task definition update)
- Smoke tests

---

## ✅ Quality Gates

- **Coverage ≥ 80%**
- **Code must compile**
- **Static analysis (SonarCloud optional)**
- **No critical security issues (Trivy, `npm audit`)**

---

## 🚀 Deployment Strategy

- Use ECS Fargate
- Canary or rolling deployments (optional via task revision)
- Manual approval before production deploys (for `main`)

---

## 📊 Monitoring and Observability

- **Logs**: AWS CloudWatch Logs per service
- **Health checks**: `/actuator/health` endpoint
- **Metrics**: Custom with Prometheus (optional)
- **Alerts**: CloudWatch Alarms or SNS notifications

---

*This CI/CD pipeline ensures each Neofile microservice is delivered with quality, control, and automation across environments.*

