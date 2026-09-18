# Principios de diseño (SOLID)

## Objetivo

Documentar cómo se aplican los principios SOLID en Stella Education, organizado con arquitectura hexagonal en los servicios Go y en los workers Python, y con _features architecture_ en el frontend, para que cada módulo se programe de forma uniforme y las reglas de negocio no dependan de la tecnología.

## Contexto

- **Estilo:** microservicios orientados a eventos.
- **Enfoque interno:** hexagonal (dominio, puertos, servicios de aplicación, adaptadores).
- **Módulo de referencia:** Documentos, porque integra PostgreSQL, Kafka, Redis, almacenamiento de objetos, el worker Python y Firma Perú.

## SOLID en el módulo Documentos

| Principio                         | Dónde se aplica           | Cómo se cumple en Stella Education                                                                                                                                 | Archivos                                                                                                  |
| --------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- |
| **S** · Responsabilidad única     | Todas las capas           | El _handler_ traduce HTTP, el caso de uso coordina, la entidad `Document` controla sus estados, el repositorio guarda en PostgreSQL y el worker solo genera el PDF | `document_handler.go`, `request_documents.go`, `document.go`, `document_repository.go`, `generate_pdf.py` |
| **O** · Abierto/cerrado           | Adaptadores y estrategias | Para un nuevo esquema de calificación o una nueva plantilla de documento se agrega una estrategia o carpeta de plantilla; los casos de uso no cambian              | `domain/grading/*.go`, `templates/report_card/<scheme>/`                                                  |
| **L** · Sustitución de Liskov     | Adaptadores de salida     | El almacenamiento S3 y el de pruebas en memoria cumplen el mismo `FileStorage`; el caso de uso funciona igual con cualquiera                                       | `s3_storage.go`, `memory_storage.go`                                                                      |
| **I** · Segregación de interfaces | Puertos                   | Cada puerto tiene solo lo necesario: `SignatureProvider` emite token y parámetros, `SignatureValidator` solo valida, `FileStorage` solo guarda y firma URL         | `signature_provider.go`, `signature_validator.go`, `file_storage.go`                                      |
| **D** · Inversión de dependencias | Aplicación                | Los casos de uso reciben interfaces por el constructor y no conocen Firma Perú, PostgreSQL ni Kafka; las implementaciones se conectan en `main.go`                 | `request_documents.go`, `cmd/server/main.go`                                                              |

## SOLID en el frontend

| Principio                 | Cómo se cumple                                                                                  |
| ------------------------- | ----------------------------------------------------------------------------------------------- |
| Responsabilidad única     | Componente muestra, hook prepara y maneja estado, acción llama al endpoint                      |
| Abierto/cerrado           | Un módulo nuevo se agrega como carpeta en `features/` y una entrada en `module-registry.ts`     |
| Segregación de interfaces | Cada componente recibe solo las _props_ que usa; los tipos viven en `types/index.ts` del módulo |
| Inversión de dependencias | Los componentes dependen de hooks, no de `fetch` ni del gateway                                 |

## Qué pasaría sin SOLID en Stella Education

| Situación real                                         | Sin SOLID                                             | Con SOLID                                            |
| ------------------------------------------------------ | ----------------------------------------------------- | ---------------------------------------------------- |
| Firma Perú cambia el formato de parámetros             | Se modifican casos de uso y _handlers_                | Solo cambia `adapter/outbound/firmaperu`             |
| Llega un cliente CETPRO con evaluación vigesimal       | Se llenan los servicios de `if tipo == "cetpro"`      | Se usa la estrategia `numeric` y la plantilla CETPRO |
| Se quiere probar la emisión de certificados            | Se necesita Kafka, PostgreSQL, S3 y Firma Perú reales | Se prueba el caso de uso con adaptadores en memoria  |
| Se cambia WeasyPrint por otro motor de PDF             | Se reescribe el worker                                | Solo se reemplaza el adaptador `Renderer`            |
| Cambia la regla de cuándo un documento se puede anular | Hay que buscarla en _handlers_, consumidores y SQL    | Está solo en la entidad `Document`                   |

## Otros principios aplicados

| Principio                            | Aplicación                                                                                                 |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| **Configuración sobre código**       | El tipo de institución se expresa como datos (plantilla, funciones, etiquetas), no como ramas en el código |
| **Alta cohesión, bajo acoplamiento** | Un servicio por contexto de negocio; comunicación por eventos                                              |
| **Inmutabilidad de lo emitido**      | Notas cerradas y documentos firmados no se modifican; se versionan o anulan                                |
| **Seguridad por defecto**            | RLS activo en todas las tablas; si falta `app.institution_id`, la consulta no devuelve filas               |
| **Contratos explícitos**             | OpenAPI para REST y JSON Schema versionado para eventos                                                    |

## Relación con la arquitectura hexagonal y los patrones

| Principio                 | Lo cumple en el proyecto                                                              |
| ------------------------- | ------------------------------------------------------------------------------------- |
| Responsabilidad única     | La separación en dominio, puertos, servicios de aplicación y adaptadores              |
| Abierto/cerrado y Liskov  | Los patrones Adapter y Strategy                                                       |
| Segregación de interfaces | Los puertos pequeños del dominio                                                      |
| Inversión de dependencias | La regla de dependencia hexagonal y la raíz de composición (`main.go`, `__main__.py`) |
