# Patrones de diseño

## Objetivo

Documentar los patrones de diseño aplicados en Stella Education, indicando dónde se usan, qué problema resuelven y con qué tecnología se implementan, para que el equipo programe de forma uniforme.

## Contexto

- **Estilo:** microservicios orientados a eventos (Go + Kafka) con workers Python.
- **Enfoque interno:** arquitectura hexagonal en backend; _features architecture_ en frontend.
- **Base:** diagrama de componentes (C4 nivel 3).

## Patrones de arquitectura distribuida

| Patrón               | Dónde se usa                      | Problema que resuelve                                                                   | Se implementa con                                                                                |
| -------------------- | --------------------------------- | --------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| API Gateway          | Entrada a todos los servicios     | Que el frontend no conozca cada servicio y que la seguridad se aplique en un solo lugar | Servicio `gateway` en Go (Fiber)                                                                 |
| Database per Service | Todos los servicios               | Que un servicio no dependa del esquema de otro                                          | Una base lógica de PostgreSQL por servicio                                                       |
| Transactional Outbox | Todo servicio que publica eventos | Publicar el evento solo si la transacción se confirmó, sin perder eventos               | Tabla `outbox` + `outbox_relay` hacia Kafka                                                      |
| Idempotent Consumer  | Todo consumidor de Kafka          | Kafka puede entregar un mensaje dos veces                                               | Tabla `processed_event (event_id)` en el consumidor                                              |
| Competing Consumers  | `pdf-worker`                      | Repartir miles de trabajos de PDF entre varias réplicas                                 | Grupo de consumidores de Kafka                                                                   |
| Saga (coreografía)   | Matrícula ↔ Finanzas ↔ Académico  | Una matrícula toca varios servicios sin transacción distribuida                         | Eventos `enrollment.created`, `finance.charge.created`, `enrollment.withdrawn` como compensación |
| Cache-Aside          | Contexto del servicio, permisos   | Evitar consultar Organización e Identidad en cada petición                              | Redis con invalidación por `tenant.service.features-changed.v1`                                  |
| Dead Letter          | Workers y consumidores            | Un mensaje defectuoso no debe bloquear la partición                                     | Tópico `documents.pdf.failed.v1` y tópicos `*.dlq`                                               |

## Patrones de diseño en el código

| Patrón                 | Dónde se usa                                        | Problema que resuelve                                                            | Archivos en el código                                                             |
| ---------------------- | --------------------------------------------------- | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| Ports & Adapters       | Todos los servicios Go y el worker                  | El negocio no debe conocer la tecnología                                         | `core/port/*.go`, `adapter/outbound/**`                                           |
| Adapter                | Documentos → Firma Perú                             | Firma Perú tiene su propio formato de parámetros y token                         | `port/signature_provider.go`, `adapter/outbound/firmaperu/client.go`              |
| Adapter                | Finanzas → Pasarela de pagos y OSE                  | Cada proveedor tiene su propia API                                               | `port/payment_gateway.go`, `adapter/outbound/niubiz/…`, `adapter/outbound/ose/…`  |
| Adapter                | Integraciones → SIAGIE, RENIEC                      | Formatos y APIs del Estado                                                       | `adapter/outbound/siagie/…`, `adapter/outbound/reniec/…`                          |
| Repository             | Todos los servicios → PostgreSQL                    | El negocio no debe conocer SQL ni RLS                                            | `port/document_repository.go`, `adapter/outbound/postgres/document_repository.go` |
| Unit of Work           | Casos de uso que escriben datos y eventos           | Guardar el cambio y el evento en una sola transacción, con la institución fijada | `adapter/outbound/postgres/tx.go`                                                 |
| Strategy               | Evaluación: esquema de calificación                 | Colegio, CETPRO, CEBE y academia califican distinto                              | `domain/grading/competency.go`, `numeric.go`, `descriptive.go`, `score.go`        |
| Strategy               | Worker: plantilla de documento                      | La libreta cambia según el esquema de calificación                               | `templates/report_card/<scheme>/` elegido por `job.template`                      |
| Factory                | Organización: creación de servicios desde plantilla | Crear un servicio con sus funciones, etiquetas y esquema por defecto             | `domain/service_factory.go`                                                       |
| State                  | Documentos: ciclo de vida del documento             | Evitar transiciones inválidas (firmar un documento anulado)                      | `domain/document.go`                                                              |
| Decorator              | Organización: contexto del servicio                 | Agregar caché sin modificar el repositorio                                       | `adapter/outbound/redisclient/cached_service_context.go`                          |
| Observer (por eventos) | Evaluación → Documentos, Bienestar, Comunicación    | Reaccionar al cierre de un corte sin acoplar servicios                           | Eventos de Kafka y consumidores                                                   |
| Feature Toggle         | Gateway, servicios y frontend                       | Activar módulos por servicio educativo                                           | `tenant.service_feature`, _middleware_ `RequireFeature`, `module-registry.ts`     |
| Composition Root       | `main.go` y `__main__.py`                           | Un solo lugar donde se crean y conectan las dependencias                         | `cmd/server/main.go`, `src/pdf_worker/__main__.py`                                |

## Cómo funciona cada patrón clave

| Patrón                  | En pocas palabras                                                                                                                       |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| Outbox                  | El caso de uso guarda el documento **y** el evento en la misma transacción. Un proceso aparte lee la tabla `outbox` y lo envía a Kafka. |
| Idempotent Consumer     | Antes de procesar un evento, el consumidor revisa si ya guardó su `event_id`; si es así, lo ignora.                                     |
| Strategy (calificación) | El servicio dice `scheme.Validate(valor)` y `scheme.Final(notas)`; cada esquema implementa su propia regla.                             |
| Adapter (Firma Perú)    | El caso de uso dice `BuildParams(documento)`; el adaptador obtiene el token y arma el JSON que espera el agente.                        |
| Feature Toggle          | El gateway responde `403 FEATURE_DISABLED` si la función no está activa; el frontend ni siquiera muestra el módulo.                     |

## Beneficio

- Cambiar Firma Perú, la pasarela, el OSE o el motor de PDF solo afecta a su adaptador.
- Un tipo nuevo de institución se resuelve con una plantilla y, como máximo, una nueva estrategia de calificación o plantilla de documento.
- Los servicios se comunican sin depender de que el otro esté disponible.

## Patrones previstos para siguientes iteraciones

| Patrón          | Dónde se aplicaría                          | Cuándo                                                         |
| --------------- | ------------------------------------------- | -------------------------------------------------------------- |
| CQRS            | Portal del apoderado y reportes             | Cuando las consultas de lectura crezcan más que las escrituras |
| Circuit Breaker | Llamadas a SIAGIE, RENIEC, OSE y Firma Perú | Al pasar a producción con proveedores reales                   |
| Saga orquestada | Admisión → Matrícula → Finanzas             | Si el proceso agrega pasos con compensaciones complejas        |
| BFF             | Aplicación móvil                            | Si se agrega una app móvil con necesidades distintas           |
