# Arquitectura inicial del sistema

## Diagrama de arquitectura

```mermaid
flowchart TD

%% =========================
%% ACTORES
%% =========================
subgraph ACTORES["ACTORES"]
    Gestion["Promotor · Director<br/>Coordinador · Secretaría · Tesorería"]
    Docente["Docente · Tutor<br/>Profesional EBE"]
    Portal["Estudiante · Apoderado"]
    Verificador["Verificador externo"]
end

%% =========================
%% PRESENTACIÓN
%% =========================
subgraph PRESENTACION["PRESENTACIÓN"]
    Web["Aplicación web<br/>Next.js"]
    Gateway["API Gateway<br/>Go"]
end

%% =========================
%% MICROSERVICIOS
%% =========================
subgraph NEGOCIO["MICROSERVICIOS (Go · hexagonal)"]
    Identidad["Identidad"]
    Organizacion["Organización<br/>plantillas · funciones"]
    Estructura["Estructura<br/>curricular"]
    Personas["Personas"]
    Matricula["Admisión y<br/>matrícula"]
    Academico["Académico"]
    Evaluacion["Evaluación"]
    Asistencia["Asistencia"]
    Aula["Aula virtual"]
    Simulacros["Simulacros"]
    Bienestar["Bienestar e<br/>inclusión"]
    Finanzas["Finanzas"]
    Comunicacion["Comunicación"]
    Documentos["Documentos<br/>y firma"]
    Integraciones["Integraciones"]
end

%% =========================
%% PROCESAMIENTO ASÍNCRONO
%% =========================
subgraph ASYNC["MENSAJERÍA Y WORKERS"]
    Kafka["Apache Kafka"]
    Worker["Workers PDF<br/>Python · uv"]
end

%% =========================
%% DATOS
%% =========================
subgraph DATOS["DATOS"]
    BD["PostgreSQL<br/>BD por servicio + RLS"]
    Redis["Redis"]
    Objetos["Almacenamiento<br/>de objetos"]
end

%% =========================
%% SISTEMAS EXTERNOS
%% =========================
subgraph EXTERNOS["SISTEMAS EXTERNOS"]
    FirmaPeru["Agente y servicio<br/>Firma Perú"]
    SIAGIE["SIAGIE"]
    RENIEC["RENIEC"]
    SUNAT["SUNAT / OSE"]
    Pasarela["Pasarela de pagos"]
    Mensajeria["Correo / WhatsApp"]
end

%% =========================
%% FLUJO PRINCIPAL
%% =========================
ACTORES --> Web
Web --> Gateway
Gateway --> NEGOCIO
NEGOCIO <--> Kafka
Kafka --> Worker
NEGOCIO --> BD
NEGOCIO --> Redis
Gateway --> Redis
Worker --> Objetos
Documentos --> Objetos

%% =========================
%% INTEGRACIONES EXTERNAS
%% =========================
Web -. invocador .-> FirmaPeru
FirmaPeru -. descarga y sube PDF .-> Gateway
Documentos --> FirmaPeru
Integraciones --> SIAGIE
Integraciones --> RENIEC
Finanzas --> SUNAT
Finanzas --> Pasarela
Comunicacion --> Mensajeria

%% =========================
%% ESTILOS
%% =========================
style ACTORES fill:#222,stroke:#fff,stroke-width:2px,color:#fff
style PRESENTACION fill:#222,stroke:#fff,stroke-width:2px,color:#fff
style NEGOCIO fill:#222,stroke:#fff,stroke-width:2px,color:#fff
style ASYNC fill:#222,stroke:#fff,stroke-width:2px,color:#fff
style DATOS fill:#222,stroke:#fff,stroke-width:2px,color:#fff
style EXTERNOS fill:#222,stroke:#fff,stroke-width:2px,color:#fff
```

## Descripción

La arquitectura inicial se organiza en cinco bloques:

- **Presentación:** la aplicación web en Next.js (organizada por _features_) y el **API Gateway** en Go, única puerta de entrada. El gateway autentica, identifica la institución por el subdominio y el token, verifica que la función solicitada esté activa en el servicio educativo y enruta al microservicio.
- **Microservicios:** cada módulo funcional es un servicio Go independiente con arquitectura hexagonal y su propia base de datos lógica. El servicio **Organización** es el dueño de las plantillas, funciones activas y etiquetas que hacen que los módulos se adecuen a cada tipo de institución.
- **Mensajería y workers:** Kafka transporta los eventos de negocio entre servicios y los trabajos de generación de PDF. Los **workers Python** (gestionados con `uv`) consumen esos trabajos, generan el PDF y lo guardan en el almacenamiento de objetos.
- **Datos:** PostgreSQL con una base lógica por servicio y RLS por `institution_id`; Redis para caché, sesiones, límites de peticiones y avance de procesos; almacenamiento de objetos compatible con S3 para los PDF y adjuntos.
- **Sistemas externos:** el **agente Firma Perú** firma los documentos en la computadora del firmante; SIAGIE, RENIEC, SUNAT (vía OSE), la pasarela de pagos y los proveedores de correo y WhatsApp se integran mediante adaptadores.

## Flujo principal: emisión de un certificado firmado

```mermaid
flowchart LR
    A["Secretaría solicita<br/>certificados"] --> B["Documentos registra la<br/>solicitud y publica trabajos"]
    B --> C["Kafka<br/>documents.pdf.requested"]
    C --> D["Worker Python genera PDF<br/>y lo sube al almacenamiento"]
    D --> E["Kafka<br/>documents.pdf.generated"]
    E --> F["Documentos marca<br/>'listo para firmar'"]
    F --> G["Director firma con<br/>el agente Firma Perú"]
    G --> H["Documentos guarda la versión<br/>firmada e inmutable"]
    H --> I["Kafka<br/>documents.document.signed"]
    I --> J["Comunicación avisa al<br/>estudiante / apoderado"]
```
