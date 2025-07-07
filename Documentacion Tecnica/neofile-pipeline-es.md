# 🔄 Pipeline CI/CD - Sistema de Gestión Documental Neofile

## 📋 Tabla de Contenido

1. [Resumen](#resumen)
2. [Arquitectura del Pipeline](#arquitectura-del-pipeline)
3. [Flujo de Desarrollo](#flujo-de-desarrollo)
4. [Estrategia de Entornos](#estrategia-de-entornos)
5. [Etapas del Pipeline](#etapas-del-pipeline)
6. [Puertas de Calidad](#puertas-de-calidad)
7. [Estrategia de Despliegue](#estrategia-de-despliegue)
8. [Monitoreo y Observabilidad](#monitoreo-y-observabilidad)

---

## 🎯 Resumen

Este documento describe la estrategia de Integración Continua y Entrega Continua (CI/CD) para Neofile. El objetivo es garantizar alta calidad de código, pruebas automatizadas y despliegues confiables para los microservicios.

### Objetivos del Pipeline

- Validaciones automatizadas de calidad (pruebas, lint, cobertura)
- Despliegues confiables en desarrollo, QA y producción
- Soporte para rollback
- Flujo de trabajo basado en Git usando Azure DevOps
- Contenerización por microservicio y despliegue en AWS ECS

---

## 🏗️ Arquitectura del Pipeline

```
[Repositorio Git (Azure DevOps)]
       │
   [Pipelines]
       │
┌────────────┐
│  Build     │ - Compila, lint, prueba
└────────────┘
       │
┌────────────┐
│  Package   │ - Construye Docker + etiquetas
└────────────┘
       │
┌────────────┐
│  Deploy    │ - Despliega a ECS con definición de tarea
└────────────┘
```

Tecnologías:

- Control de versiones: Git (Azure Repos)
- CI/CD: Azure Pipelines
- Registro de contenedores: AWS ECR
- Despliegue: AWS ECS (Fargate)
- Monitoreo: CloudWatch

---

## 🔄 Flujo de Desarrollo

### Estrategia Git

- Ramas: `main`, `develop`, `feature/*`, `hotfix/*`
- Pull Requests: requeridos hacia `develop` y `main`

### Formato de Commits

```
<tipo>(<área>): <mensaje>
```

Ejemplo:

```
feat(documents): agregar endpoint de clasificación con GPT
```

---

## 🌍 Estrategia de Entornos

| Entorno      | Propósito              | Rama        | URL Ejemplo          |
|--------------|------------------------|-------------|----------------------|
| Desarrollo   | Pruebas de desarrollo  | `develop`   | `dev.neofile.com`    |
| QA           | Validación funcional   | `release/*` | `qa.neofile.com`     |
| Producción   | Tráfico real           | `main`      | `app.neofile.com`    |

- Configuración vía `application-dev.yml`, `application-prod.yml`

---

## 🔄 Etapas del Pipeline

### 1. Build

- Compilar código
- Ejecutar pruebas unitarias
- Validación con lint

### 2. Test

- Pruebas de integración (por servicio)
- Pruebas de contrato (si aplica)

### 3. Package

- Construcción de imagen Docker
- Push a ECR con etiquetas: `latest`, `commit_sha`

### 4. Deploy

- Despliegue en ECS (actualización de task definition)
- Pruebas de humo

---

## ✅ Puertas de Calidad

- **Cobertura ≥ 80%**
- **El código debe compilar**
- **Análisis estático (SonarCloud opcional)**
- **Sin vulnerabilidades críticas (Trivy, audit)**

---

## 🚀 Estrategia de Despliegue

- Usar ECS Fargate
- Despliegue canario o rolling (opcional con revisiones)
- Aprobación manual antes de despliegue a producción (`main`)

---

## 📊 Monitoreo y Observabilidad

- **Logs**: AWS CloudWatch por servicio
- **Health checks**: endpoint `/actuator/health`
- **Métricas**: Personalizadas con Prometheus (opcional)
- **Alertas**: CloudWatch Alarms o notificaciones vía SNS

---

*Este pipeline CI/CD garantiza que cada microservicio de Neofile se entregue con calidad, control y automatización en todos los entornos.*