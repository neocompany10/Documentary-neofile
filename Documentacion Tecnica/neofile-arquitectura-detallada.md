# 🏗️ Visión General de la Arquitectura - Sistema de Gestión Documental Neofile

## 📋 Tabla de Contenido
1. [Contexto del Sistema](#contexto-del-sistema)
2. [Vistas Arquitectónicas](#vistas-arquitectónicas)
3. [Stack Tecnológico](#stack-tecnológico)
4. [Decisiones de Diseño](#decisiones-de-diseño)
5. [Atributos de Calidad](#atributos-de-calidad)
6. [Costos y Uso de Tokens de IA](#costos-y-uso-de-tokens-de-ia)
7. [Evaluación de Riesgos](#evaluación-de-riesgos)

---

## 🎯 Contexto del Sistema

Neofile es un sistema inteligente de gestión documental desarrollado por Gestión Documental de Colombia S.A.S. Permite a las empresas clasificar, buscar y organizar documentos digitales usando automatización con inteligencia artificial, apoyando una gestión de información eficiente, segura y transparente.

### Capacidades Empresariales Clave
- **Carga y Clasificación de Documentos**
- **Gestión de Expedientes** (agrupación por caso o asunto)
- **Gestión de Usuarios**
- **Búsqueda y Recuperación con Lenguaje Natural**
- **Integración con Microsoft 365 y Azure DevOps**

---

## 🏛️ Vistas Arquitectónicas

### 1. Vista de Negocio

#### Principales Stakeholders
- **Usuarios internos**: Administradores, asistentes, responsables
- **Usuarios externos**: Clientes o terceros que entregan documentos
- **Equipo técnico**: Desarrolladores y DevOps

#### Procesos de Negocio
1. **Envío de Documentos**: El usuario sube archivos PDF.
2. **Clasificación con IA**: El sistema detecta tipos documentales (ej: cédula, contrato, acta).
3. **Estructuración del Expediente**: Los documentos se organizan dentro de un expediente digital.
4. **Búsqueda con prompts**: El usuario busca usando lenguaje natural.
5. **Auditoría**: Se rastrea el ciclo de vida del documento.

### 2. Vista Lógica

#### Componentes Principales

##### Capa de Dominio
- **Dominio de Documentos**: Ingesta, extracción y etiquetado de metadatos.
- **Dominio de IA**: Interacción con GPT-4o para clasificación y análisis.
- **Dominio de Expedientes**: Agrupa documentos por lógica de caso.
- **Dominio de Usuario**: Autenticación y control de accesos.

##### Capa de Aplicación
- Servicio de Clasificación
- Servicio de Búsqueda por Prompt
- Servicio de Carga de Documentos
- Servicio de Gestión de Expedientes

##### Capa de Infraestructura
- **Procesador PDF**: Extrae texto (PDFBox)
- **Gateway IA**: Llama a API de OpenAI
- **Almacenamiento**: AWS S3
- **Base de Datos**: PostgreSQL (AWS RDS)
- **Mensajería (opcional)**: RabbitMQ

### 3. Vista Física

#### Arquitectura de Despliegue
```
┌──────────────────────┐
│   Api Gateway        │
└──────────┬───────────┘
           │
┌──────────▼───────────┐
│ Balanceador de Carga │
└──────────┬───────────┘
           │
 ┌─────────▼─────────┐
 │   Frontend Angular│
 └─────────┬─────────┘
           │
 ┌─────────▼─────────┐
 │  Microservicios   │ (users-service, documents-service, ai-service)
 └─────────┬─────────┘
           │
 ┌─────────▼─────────┐
 │ PostgreSQL (RDS)  │
 └───────────────────┘
 ┌─────────▼─────────┐
 │ Almacenamiento S3 │
 └───────────────────┘
```

---

## 🛠️ Stack Tecnológico

### Frontend
- **Framework**: Angular
- **Hosting**: Vercel, S3 + CloudFront o Azure Static Web Apps

### Backend
- **Lenguaje**: Java 21
- **Framework**: Spring Boot + WebFlux
- **Base de datos**: PostgreSQL (AWS RDS)
- **Integración IA**: OpenAI GPT-4o via REST - trebol OCR
- **Parser PDF**: Apache PDFBox
- **Eventos (opcional)**: RabbitMQ

### DevOps
- **CI/CD**: Azure DevOps Pipelines
- **Control de versiones**: Azure Repos (Git)
- **Monitoreo**: AWS CloudWatch
- **Almacenamiento**: AWS S3
- **Identidad**: Microsoft 365 + Azure AD

---

## 🎯 Decisiones de Diseño

### 1. Arquitectura Hexagonal
- Uso de puertos y adaptadores para modularidad y pruebas y DDD (Driver - Domain - Design).

### 2. Procesamiento Fragmentado con IA
- Dividir PDF en bloques de 10 páginas antes de enviar a GPT-4o.
- Resultados se consolidan en el expediente.

### 3. Búsqueda por Prompt
- Los usuarios realizan búsquedas con lenguaje natural.

### 4. Control de Tokens
- Límite de entrada para controlar costos.
- GPT-4o se selecciona por relación costo/rendimiento.

---

## 📊 Atributos de Calidad

- **Escalabilidad**: AWS ECS permite escalar horizontalmente.
- **Seguridad**: IAM, Secrets Manager y HTTPS forzado, JWT.
- **Resiliencia**: Lógica de reintentos, logs, circuit breakers.
- **Trazabilidad**: Auditoría y clasificación de documentos.
- **Mantenibilidad**: Servicios modulares + CI/CD automático.

---

## 💰 Costos y Uso de Tokens de IA

### Precios GPT-4o (API)
| Tipo     | Precio por 1,000 tokens |
|----------|--------------------------|
| Entrada  | $0.005                   |
| Salida   | $0.015                   |

### Estimación de tokens
- 1 página ≈ 400 tokens
- 100 páginas ≈ 40,000 tokens entrada ≈ $0.20 USD por documento

### Estimación Mensual (COP)
| Documentos/mes | USD aprox | COP aprox (4,200) |
|----------------|-----------|-------------------|
| 100            | $20       | ~84,000           |
| 500            | $100      | ~420,000          |

---

## ⚠️ Evaluación de Riesgos

### Riesgo Alto
- **Alto uso de tokens** (mitigado con fragmentación)
- **Dependencia de OpenAI** (mitigado con monitoreo de consumo)

### Riesgo Medio
- **Diversidad documental** puede afectar precisión
- **Facturación variable** sin límites establecidos

### Riesgo Bajo
- **Herramientas** gratuitas para MVP (Azure + AWS)
- **Escalado** fluido con ECS, RDS y S3

---

*Este documento representa la arquitectura actual y estrategia de escalado de Neofile, desde MVP hasta producción, integrando automatización documental mediante IA.*