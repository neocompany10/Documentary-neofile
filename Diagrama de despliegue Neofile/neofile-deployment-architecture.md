# 🌐 Documentación Técnica - Diagrama de Despliegue: Sistema Neofile

---

## 📑 Índice

1. Introducción
2. Enfoque arquitectónico
3. Diagrama gráfico de despliegue
4. Justificación técnica de cada elemento
5. Caso práctico de implementación
6. Conclusión
7. Pregunta para discusión
8. Referencia (APA 7.ª edición)

---

## 📘 1. Introducción

Este documento describe el diagrama de arquitectura de despliegue de la aplicación web Neofile, un sistema de gestión documental basado en microservicios. Se busca ilustrar cómo se orquestan los servicios, cómo interactúan entre sí y con la nube (AWS), y qué tecnologías intervienen en la solución.

---

## 🧠 2. Enfoque arquitectónico

Neofile utiliza una arquitectura basada en:

- Microservicios desacoplados
- Despliegue en contenedores sobre Kubernetes (EKS)
- Enrutamiento mediante API Gateway + Load Balancer
- Persistencia distribuida en PostgreSQL (por microservicio)
- Clasificación documental mediante inteligencia artificial (IA) activada vía AWS Lambda
- Almacenamiento de archivos en Amazon S3

---

## 🗺️ 3. Diagrama gráfico de despliegue

![Diagrama de arquitectura de despliegue](Arquitectura%20Neofile%20light.drawio.png)

---

## ⚙️ 4. Justificación técnica de los elementos

| Componente                | Justificación técnica                                         |
|--------------------------|---------------------------------------------------------------|
| **API Gateway + LB**     | Gestiona el ingreso de tráfico externo mediante HTTPS         |
| **Kubernetes (EKS)**     | Orquesta los contenedores de los microservicios               |
| **Amazon S3**            | Almacenamiento de archivos originales (PDF, imágenes, etc.)   |
| **PostgreSQL (RDS)**     | Persistencia distribuida por microservicio                    |
| **SQS + Lambda**         | Clasificación documental asincrónica usando GPT               |
| **user-services**        | Autenticación y datos de usuarios                             |
| **centralfile-services** | Gestión, metadatos y archivado de documentos                  |
| **managefile-services**  | Registro de cambios de estado (revisado, archivado, etc.)     |
| **documentaryflow-services** | Encaminamiento de flujos y asignaciones                |
| **singlewindow-services**| Entrada de radicaciones desde usuarios internos o externos    |
| **ia-agent-lambda**      | Analiza documentos con IA y responde vía HTTP                 |

---

## 🧪 5. Caso práctico de implementación

**Escenario: Subida de documento clasificado automáticamente**

1. El cliente carga un documento desde el frontend a través del `API Gateway`.
2. La petición llega al `singlewindow-service` o `centralfile-service`.
3. El servicio guarda el documento en Amazon S3 y la metadata en PostgreSQL.
4. Se publica un mensaje en SQS.
5. La Lambda (`ia-agent-lambda`) se activa y consulta S3.
6. Clasifica el documento con OpenAI GPT y actualiza los metadatos en `centralfile-service`.

---

## ✅ 6. Conclusión

Este diseño permite una solución escalable, desacoplada, segura y moderna. Utilizar componentes administrados de AWS y contenedores en Kubernetes reduce la complejidad operativa y mejora la mantenibilidad del sistema.

---

## 💬 7. Pregunta para discusión

¿Cómo podríamos adaptar este diseño si quisiéramos migrar ciertos microservicios a un modelo serverless completo?

---

## 📚 8. Referencia (APA 7.ª edición)

Newman, S. (2021). *Building Microservices: Designing Fine-Grained Systems* (2nd ed.). O'Reilly Media.

---