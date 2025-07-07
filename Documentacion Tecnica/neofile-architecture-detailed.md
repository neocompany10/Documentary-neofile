# 🏗️ Architecture Overview - Neofile Document Management System

## 📋 Table of Contents
1. [System Context](#system-context)
2. [Architectural Views](#architectural-views)
3. [Technology Stack](#technology-stack)
4. [Design Decisions](#design-decisions)
5. [Quality Attributes](#quality-attributes)
6. [Cost and AI Token Usage](#cost-and-ai-token-usage)
7. [Risk Assessment](#risk-assessment)

---

## 🎯 System Context

Neofile is an intelligent document management system developed by Gestión Documental de Colombia S.A.S. It enables businesses to classify, search, and organize digital documents using AI-powered automation, supporting efficient, secure, and transparent information management.

### Core Business Capabilities
- **Document Upload and Classification**
- **Expedient Management** (Organizing documents by case or topic)
- **User Management**
- **Search and Retrieval with Natural Language Prompts**
- **Integration with Microsoft 365 and Azure DevOps**

---

## 🏛️ Architectural Views

### 1. Business View

#### Key Stakeholders
- **Internal Users**: Admins, Assistants, Managers
- **External Users**: Clients or third parties submitting documentation
- **IT Staff**: Developers and DevOps maintaining the system

#### Business Processes
1. **Document Submission**: Users upload PDF files.
2. **AI Classification**: System detects document types (e.g., ID, Contract, Act).
3. **Expedient Structuring**: Classified documents are linked to digital case files.
4. **Prompt-based Search**: Users query documents with natural language.
5. **Audit and History**: Document lifecycle is tracked for traceability.

### 2. Logical View

#### Core Components

##### Domain Layer
- **Document Domain**: Handles PDF ingestion, extraction, and metadata tagging.
- **AI Domain**: Interacts with GPT-4o for classification and analysis.
- **Expedient Domain**: Groups documents into logical units or cases.
- **User Domain**: Manages authentication and role-based access.

##### Application Layer
- **Classification Service**
- **Prompt Search Service**
- **Document Upload Service**
- **Expedient Management Service**

##### Infrastructure Layer
- **PDF Processor**: Extracts text from PDF using PDFBox
- **AI Gateway**: Connects to OpenAI GPT-4o API
- **Storage**: AWS S3 for file storage
- **Database**: PostgreSQL (AWS RDS)
- **Message Broker**: RabbitMQ (optional for async processing)

### 3. Physical View

#### Deployment Architecture
```
┌──────────────────────┐
│      Load Balancer   │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│     API Gateway      │
└──────────┬───────────┘
           │
 ┌─────────▼─────────┐
 │   Angular Frontend│
 └─────────┬─────────┘
           │
 ┌─────────▼─────────┐
 │  Spring Boot Apps │ (users-service, documents-service, ai-service)
 └─────────┬─────────┘
           │
 ┌─────────▼─────────┐
 │  PostgreSQL (RDS) │
 └───────────────────┘
 ┌─────────▼─────────┐
 │  AWS S3 Storage   │
 └───────────────────┘
```

---

## 🛠️ Technology Stack

### Frontend
- **Framework**: Angular
- **Hosting**: Vercel, S3 + CloudFront, or Azure Static Web Apps

### Backend
- **Language**: Java 21
- **Framework**: Spring Boot + WebFlux
- **Database**: PostgreSQL (AWS RDS)
- **AI Integration**: OpenAI GPT-4o via REST
- **PDF Parsing**: Apache PDFBox
- **Events (Optional)**: RabbitMQ

### DevOps
- **CI/CD**: Azure DevOps Pipelines
- **Source Control**: Azure Repos (Git)
- **Monitoring**: CloudWatch
- **Storage**: AWS S3
- **Identity**: Microsoft 365 + Azure AD

---

## 🎯 Design Decisions

### 1. Clean Hexagonal Architecture
- Ports and adapters for maintainable and testable code.

### 2. Fragment-based AI Processing
- Large PDFs are split into 10-page segments before GPT-4o is called.
- Results are merged and structured into expedients.

### 3. Prompt-driven Query Interface
- Users search documents using plain language queries.

### 4. Token Usage Control
- Input limited to key sections to reduce cost.
- GPT-4o is chosen for best price-to-performance.

---

## 📊 Quality Attributes

- **Scalability**: AWS ECS allows horizontal scaling.
- **Security**: IAM roles + Secrets Manager + HTTPS enforced.
- **Resilience**: Retry logic, logging, and circuit breakers.
- **Traceability**: Document audit logs + classified metadata.
- **Maintainability**: Modular services + automated CI/CD.

---

## 💰 Cost and AI Token Usage

### GPT-4o Pricing (API)
| Type   | Cost per 1K tokens |
|--------|---------------------|
| Input  | $0.005              |
| Output | $0.015              |

### Token Estimation
- 1 page ≈ 400 tokens
- 100 pages = 40,000 tokens input ≈ $0.20 per doc

### Monthly Estimate (COP)
| Documents/mo | Approx USD | Approx COP (4,200) |
|--------------|------------|---------------------|
| 100          | $20        | ~84,000 COP         |
| 500          | $100       | ~420,000 COP        |

---

## ⚠️ Risk Assessment

### High Risk
- **High token usage** from large documents (mitigated with segmentation)
- **API dependency** on OpenAI (mitigated with usage monitoring)

### Medium Risk
- **Document diversity** may affect classification accuracy
- **Billing drift** if token usage is uncontrolled

### Low Risk
- **Tooling**: Azure DevOps and AWS have free tiers for MVP
- **Scaling**: ECS, RDS, S3 scale smoothly with load

---

*This document reflects the current system architecture and strategy for scaling Neofile from MVP to production with AI-powered document automation.*