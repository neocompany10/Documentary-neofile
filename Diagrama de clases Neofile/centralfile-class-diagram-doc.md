# 📘 Documentación Técnica - Diagrama de Clases: centralfile-service

---

## 📑 Índice

1. Introducción
2. Enfoque arquitectónico
3. Diagrama de clases
4. Justificación técnica de los componentes
5. Caso práctico de implementación
6. Conclusión
7. Pregunta para discusión
8. Referencia (APA 7.ª edición)

---

## 🧠 1. Introducción

Este documento detalla el diseño orientado a objetos del microservicio `centralfile-service`, perteneciente a la arquitectura de la plataforma Neofile. Se utiliza un enfoque limpio, aplicando arquitectura hexagonal, principios SOLID y patrones de diseño del catálogo de GoF (Gang of Four).

---

## 🧩 2. Enfoque arquitectónico

El diseño aplica:

- Arquitectura Hexagonal (Puertos y Adaptadores)
- Principios SOLID (Single Responsibility, Dependency Inversion, Interface Segregation)
- Patrones de diseño clásicos (GoF), entre ellos:

| Patrón GoF           | Aplicación en el diseño                                 |
|----------------------|----------------------------------------------------------|
| **Factory Method**   | Creación de casos de uso en `UseCaseConfig`             |
| **Adapter**          | Implementaciones de gateways (`BDDocumentRepository`, `S3StorageAdapter`, `LambdaClassifierAdapter`) |
| **Strategy**         | Uso potencial en `ClassificationGateway` para múltiples clasificadores IA |
| **Facade**           | `DocumentService` actúa como fachada entre handler y casos de uso |

El dominio permanece desacoplado de la infraestructura y las dependencias se inyectan desde la capa `application`.

---

## 🖼️ 3. Diagrama gráfico

![Diagrama de Clases](Diagrama%20de%20clases.png)

---

## ⚙️ 4. Justificación técnica de cada componente

| Clase / Interfaz                 | Función técnica                                                                  |
|----------------------------------|----------------------------------------------------------------------------------|
| `Document`                      | Entidad del dominio con validación propia                                        |
| `DocumentGateway`               | Puerto de persistencia (interfaz)                                               |
| `StorageGateway`                | Puerto de almacenamiento en S3                                                  |
| `ClassificationGateway`         | Puerto para clasificación con IA                                                |
| `UploadDocumentUseCase`         | Caso de uso que orquesta la subida y clasificación del documento                |
| `ArchiveDocumentUseCase`        | Caso de uso que cambia el estado del documento a archivado                      |
| `BDDocumentRepository`          | Adaptador concreto hacia PostgreSQL (`@Repository`)                             |
| `S3StorageAdapter`              | Adaptador para subida de archivos a S3 (`@Component`)                           |
| `LambdaClassifierAdapter`       | Adaptador que envía documentos a Lambda vía SQS para clasificación GPT          |
| `UseCaseConfig`                 | Ensamblador de dependencias, aplica **Factory Method**                          |
| `DocumentService`               | Coordina los casos de uso, aplica **Facade**                                    |
| `DocumentHandler`               | Punto de entrada WebFlux (`@Component`) que expone los endpoints HTTP           |

---

## 🧪 5. Caso práctico

**Escenario:** un usuario carga un archivo PDF al sistema.

1. `DocumentHandler` recibe la petición HTTP.
2. Llama a `uploadDocument()` en `DocumentService`.
3. `DocumentService` ejecuta `UploadDocumentUseCase`.
4. Se sube el archivo a S3 (`S3StorageAdapter`).
5. Se guardan los metadatos en la base de datos (`BDDocumentRepository`).
6. Se invoca `LambdaClassifierAdapter` para enviar el documento a IA.
7. El documento queda disponible para futuras operaciones.

---

## ✅ 6. Conclusión

El diseño del microservicio `centralfile-service` refleja una arquitectura bien estructurada, alineada con las mejores prácticas de ingeniería de software. Integra varios patrones de diseño de GoF de manera contextual y efectiva. Mantiene el dominio limpio, las dependencias bien gestionadas y es fácilmente extensible y testeable.

---

## 💬 7. Pregunta para discusión

¿Cómo podríamos extender este diseño si se desea soportar múltiples motores de clasificación (GPT, modelos locales, reglas), aplicando el patrón Strategy?

---

## 📚 8. Referencia (APA 7.ª edición)

Gamma, E., Helm, R., Johnson, R., & Vlissides, J. (1994). *Design Patterns: Elements of Reusable Object-Oriented Software*. Addison-Wesley.

---