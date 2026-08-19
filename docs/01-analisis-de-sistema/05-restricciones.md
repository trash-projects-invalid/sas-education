# Restricciones

Identificar las **restricciones** que condicionan las decisiones de diseño y arquitectura del sistema.

## Restricciones técnicas

| ID   | Restricción                  | Descripción                                                                                                                                     |
| ---- | ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| RC01 | **Aplicación web**           | El sistema se usa desde el navegador, en computadora y en celular.                                                                              |
| RC02 | **Frontend en Next.js**      | La aplicación web se desarrolla con Next.js (App Router) y TypeScript, organizada por _features_.                                               |
| RC03 | **Microservicios en Go**     | La lógica de negocio se implementa en microservicios escritos en Go, con arquitectura hexagonal.                                                |
| RC04 | **Workers en Python con uv** | La generación de documentos PDF se realiza con workers en Python; dependencias y entornos se gestionan con `uv` (`pyproject.toml` + `uv.lock`). |
| RC05 | **Apache Kafka**             | La comunicación asíncrona entre servicios y la cola de trabajos de documentos se realiza con Kafka.                                             |
| RC06 | **Redis**                    | Se utiliza Redis para caché, sesiones, límites de peticiones y avance de procesos.                                                              |
| RC07 | **PostgreSQL**               | Los datos transaccionales se guardan en PostgreSQL.                                                                                             |
| RC08 | **API REST/JSON**            | El frontend se comunica con el backend mediante una API REST/JSON publicada por un API Gateway.                                                 |
| RC09 | **Contenedores**             | Todos los servicios se empaquetan como imágenes Docker.                                                                                         |
| RC10 | **Control de versiones**     | El código se gestiona con Git.                                                                                                                  |

## Restricciones de negocio y normativas

| ID   | Restricción                        | Descripción                                                                                                                                                                                                                                               |
| ---- | ---------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RC11 | **Firma digital con Firma Perú**   | La firma de documentos se realiza con el **agente Firma Perú** de la PCM, en la computadora del firmante y con su certificado digital (Ley 27269 de Firmas y Certificados Digitales). La plataforma nunca recibe ni guarda la clave privada del firmante. |
| RC12 | **Protección de datos personales** | El tratamiento de datos, en especial de menores de edad, cumple la Ley 29733 y su reglamento.                                                                                                                                                             |
| RC13 | **Comprobantes electrónicos**      | Las boletas y facturas se emiten según las normas de SUNAT, a través de un OSE.                                                                                                                                                                           |
| RC14 | **Formatos MINEDU**                | Las exportaciones de modalidades reguladas siguen el formato SIAGIE vigente de cada modalidad.                                                                                                                                                            |
| RC15 | **Multiinstitución**               | Una sola plataforma (SaaS) atiende a todas las organizaciones; cada una ve solo sus datos.                                                                                                                                                                |
| RC16 | **Localización**                   | Idioma español (es-PE), zona horaria America/Lima y moneda PEN.                                                                                                                                                                                           |
