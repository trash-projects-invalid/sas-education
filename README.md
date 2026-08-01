# Stella Education

Plataforma **SaaS de gestión educativa** para cualquier tipo de centro educativo: **colegios, escuelas, academias, centros (CEBA, CEBE, PRITE, CETPRO) e institutos**. Cubre organización, matrícula, gestión académica, evaluación, asistencia, aula virtual, finanzas, comunicación y la **emisión de documentos oficiales en PDF firmados digitalmente con Firma Perú**.

## Principio rector

**Un motor académico genérico y una plantilla por tipo de institución.**

Los módulos ya están desarrollados y son los mismos para todos. Lo que cambia según el tipo de institución es:

- **qué módulos están activos** (funciones activables por servicio educativo),
- **cómo se llaman las cosas** (etiquetas: "Grado", "Semestre", "Módulo", "Ciclo"…),
- **cómo se evalúa** (competencias, descriptivo, vigesimal o puntaje).

Agregar un tipo nuevo de institución consiste en **cargar una plantilla**, no en programar.

## Stack

| Capa | Tecnología |
|---|---|
| Frontend | Next.js (App Router) · **Features architecture** |
| Microservicios | Go (Fiber) · **Arquitectura hexagonal** (puertos y adaptadores) |
| Workers de documentos | Python gestionado con **uv** · generación de PDF |
| Mensajería | Apache Kafka (eventos entre servicios y cola de trabajos de PDF) |
| Caché y sesiones | Redis |
| Base de datos | PostgreSQL (una base lógica por servicio, RLS por institución) |
| Firma digital | **Agente Firma Perú** (PCM), firma PAdES con el certificado del usuario |

## Documentación

| # | Sección | Contenido |
|---|---|---|
| 01 | [Análisis del sistema](docs/01-analisis-de-sistema/) | Contexto del negocio, actores, historias de usuario, requisitos funcionales, atributos de calidad, restricciones, drivers, decisiones y escenarios de calidad |
| 02 | [Arquitectura de software](docs/02-arquitectura-software/) | Estilo arquitectónico, enfoque arquitectónico, arquitectura inicial y componentes arquitectónicos |
| 03 | [Diseño de software](docs/03-diseno-de-software/diseno-interno/) | Diseño interno de módulos (Go, Python, Next.js), patrones de diseño y principios SOLID |
| 04 | [Modelo C4](docs/04-modelo-c4/) | Nivel 1 contexto · Nivel 2 contenedores · Nivel 3 componentes · Nivel 4 código |

### Orden de lectura sugerido

1. [`00-contexto-del-negocio.md`](docs/01-analisis-de-sistema/00-contexto-del-negocio.md): tipos de institución y cómo se adaptan los módulos.
2. [`01-actores.md`](docs/01-analisis-de-sistema/01-actores.md) → [`08-escenarios-atributos-calidad.md`](docs/01-analisis-de-sistema/08-escenarios-atributos-calidad.md).
3. [`estilo-arquitectonico.md`](docs/02-arquitectura-software/estilo-arquitectonico.md) y [`enfoque-arquitectonico.md`](docs/02-arquitectura-software/enfoque-arquitectonico.md).
4. Modelo C4, del [nivel 1](docs/04-modelo-c4/Nivel1-DiagramadeContextodelSistema.md) al [nivel 4](docs/04-modelo-c4/Nivel4-DiagramadeCodigo.md).

> Los diagramas están escritos en **Mermaid** y se muestran directamente en GitHub/GitLab.
