# Contexto del negocio

## 1. Qué es Stella Education

Stella Education es una plataforma **SaaS multiinstitución**: una sola plataforma atiende a muchas organizaciones educativas (clientes), y cada una ve únicamente su propia información.

Una **organización** (el cliente que paga la suscripción, con RUC y marca) puede tener uno o varios **servicios educativos** a la vez. Por ejemplo, una misma promotora puede tener un colegio, un CEBA anexo y una academia preuniversitaria.

| Concepto                           | Qué representa                                   | Ejemplo                                             |
| ---------------------------------- | ------------------------------------------------ | --------------------------------------------------- |
| **Organización** (`institution`)   | Cliente SaaS: RUC, marca, suscripción            | "Asociación Educativa San Martín"                   |
| **Servicio educativo** (`service`) | Unidad educativa con tipo, modalidad y plantilla | Colegio Inicial-Primaria-Secundaria; CEBA; Academia |
| **Sede** (`campus`)                | Local físico                                     | Sede central, filial norte, periférico de CEBA      |

## 2. Tipos de institución atendidos

| Tipo de servicio                            | Modalidad                         | Regulador       | Cómo se organiza                                                                                                                        |
| ------------------------------------------- | --------------------------------- | --------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **Colegio / escuela** (IE, PRONOEI)         | EBR: Educación Básica Regular     | MINEDU (SIAGIE) | Niveles Inicial, Primaria, Secundaria → ciclos → grados; año escolar por bimestres o trimestres; evaluación por competencias (AD/A/B/C) |
| **CEBA**                                    | EBA: Educación Básica Alternativa | MINEDU (SIAGIE) | Ciclos Inicial, Intermedio, Avanzado; periodos promocionales (2026-I, 2026-II); prueba de ubicación, convalidación                      |
| **CEBE / PRITE / SAANEE**                   | EBE: Educación Básica Especial    | MINEDU (SIAGIE) | Grupos adaptados; PRITE sin grados (sesiones por niño); evaluación descriptiva y Plan de Orientación Individual                         |
| **CETPRO**                                  | ETP: Técnico-Productiva           | MINEDU          | Programas de estudio → módulos → unidades didácticas; evaluación vigesimal; certificación modular                                       |
| **Instituto** (IEST, IES, EESP)             | Educación Superior                | MINEDU          | Carreras con plan de estudios versionado; semestres; créditos y prerrequisitos; matrícula por curso                                     |
| **Academia / centro de idiomas / talleres** | Libre, no regulada                | Ninguno         | Ciclos comerciales (Anual, Verano, Intensivo); grupos por área objetivo; simulacros con puntaje y ranking                               |

## 3. Conceptos genéricos y su equivalencia

El sistema trabaja con **conceptos genéricos**. La interfaz muestra la **etiqueta** que define la plantilla del servicio.

| Concepto genérico         | Colegio              | CEBA                    | CEBE / PRITE                  | CETPRO              | Instituto           | Academia        |
| ------------------------- | -------------------- | ----------------------- | ----------------------------- | ------------------- | ------------------- | --------------- |
| **Programa**              | Nivel                | PEBAJA / Alfabetización | CEBE Inicial/Primaria · PRITE | Programa de estudio | Carrera             | Área objetivo   |
| **Nivel de avance**       | Grado                | Grado                   | Grado / grupo                 | Módulo              | Semestre            | Ninguno o nivel |
| **Periodo**               | Año escolar          | Periodo promocional     | Año escolar                   | Periodo             | Semestre académico  | Ciclo comercial |
| **Corte**                 | Bimestre / trimestre | Corte                   | Bimestre / trimestre          | Unidad              | Unidad / parcial    | Semana / bloque |
| **Grupo**                 | Sección              | Sección                 | Aula / grupo                  | Grupo               | Sección             | Aula / turno    |
| **Componente curricular** | Área                 | Área                    | Área                          | Unidad didáctica    | Unidad didáctica    | Curso           |
| **Esquema de evaluación** | Competencias         | Competencias            | Descriptivo                   | Vigesimal           | Vigesimal ponderado | Puntaje         |

## 4. Cómo se adecuan los módulos según el tipo

Cada servicio educativo usa una **plantilla** (`service_template`) que define tres cosas:

1. **Funciones activas** por defecto (`service_feature`): qué módulos ve y usa el servicio.
2. **Etiquetas** de la interfaz: el frontend nunca escribe "Grado" ni "Bimestre" en el código.
3. **Esquema de calificación** por defecto y tipos de periodo y corte permitidos.

La institución puede ajustar las funciones activas dentro de lo que permite su plan de suscripción.

| Función (`feature_code`)         |    Colegio    |     CEBA      | CEBE/PRITE |  CETPRO  | Instituto | Academia |
| -------------------------------- | :-----------: | :-----------: | :--------: | :------: | :-------: | :------: |
| `siagie_export`                  |       ✓       |       ✓       |     ✓      |    ✓     |     ✓     |          |
| `cneb_competencies`              |       ✓       |       ✓       |     ✓      |          |           |          |
| `placement_test`                 |               |       ✓       |            |          |           | opcional |
| `credits_prerequisites`          |               |               |            |          |     ✓     |          |
| `course_enrollment`              |               |               |            |    ✓     |     ✓     |          |
| `inclusion_nee`                  |       ✓       |       ✓       |     ✓      | opcional | opcional  |          |
| `mock_exams_ranking`             |   opcional    |               |            |          |           |    ✓     |
| `tuition_fees`                   | si es privado | si es privado |            |    ✓     |     ✓     |    ✓     |
| `admission_process`              | si es privado |               |            |          |     ✓     | opcional |
| `lms` (aula virtual)             |       ✓       |       ✓       |  opcional  |    ✓     |     ✓     |    ✓     |
| `digital_signature` (Firma Perú) |       ✓       |       ✓       |     ✓      |    ✓     |     ✓     | opcional |
| `ai_risk` (alertas de riesgo)    |       ✓       |       ✓       |            |    ✓     |     ✓     |    ✓     |

## 5. Módulos funcionales

| Módulo                | Contenido                                                                                             | ¿Activable?                   |
| --------------------- | ----------------------------------------------------------------------------------------------------- | ----------------------------- |
| Identidad             | Cuentas, sesiones, 2FA, roles y permisos por institución                                              | Siempre activo                |
| Organización          | Organizaciones, servicios, sedes, plantillas, funciones y etiquetas                                   | Siempre activo                |
| Estructura curricular | Catálogo regulado MINEDU (solo lectura) y programas propios                                           | Siempre activo                |
| Personas              | Estudiantes, apoderados, docentes y personal no docente                                               | Siempre activo                |
| Admisión y matrícula  | Postulación, prueba de ubicación, matrícula por sección o por curso, traslados, retiros               | Parcial (admisión, ubicación) |
| Académico             | Periodos, cortes, secciones, aulas, carga docente y horarios                                          | Siempre activo                |
| Evaluación            | Esquemas de calificación, notas, libretas, actas y situación final                                    | Siempre activo                |
| Asistencia            | Asistencia de estudiantes y personal (manual o QR)                                                    | Siempre activo                |
| Aula virtual          | Recursos, tareas, foros                                                                               | Activable                     |
| Simulacros            | Exámenes, corrección, ranking                                                                         | Activable                     |
| Bienestar e inclusión | Tutoría, alertas, NEE, POI, SAANEE, PRITE                                                             | Parcial (inclusión)           |
| Finanzas              | Conceptos, cronogramas, cobros, becas, caja, comprobantes SUNAT                                       | Activable                     |
| Comunicación          | Comunicados, mensajería, notificaciones, portal de apoderados                                         | Siempre activo                |
| Documentos            | Constancias, libretas, actas, certificados en PDF; firma digital con Firma Perú; verificación pública | Firma activable               |
| Integraciones         | SIAGIE, RENIEC, SUNAT, pasarela de pagos                                                              | Activable                     |

## 6. Reglas de negocio transversales

1. **Ninguna regla depende del nombre de la institución.** Todo depende de la plantilla, la modalidad y las funciones activas del servicio.
2. **El catálogo regulado (MINEDU) es global y de solo lectura** para las instituciones. El catálogo propio pertenece a cada institución.
3. **El periodo pertenece al servicio**, no a la organización. Un colegio con CEBA anexo puede tener abiertos a la vez el año 2026 y el periodo 2026-II.
4. **Nada académico ni financiero se borra.** Retiros, traslados y anulaciones son cambios de estado auditados.
5. **Cerrar un corte bloquea sus notas**, y cerrar el periodo genera la situación final y las actas.
6. **Un documento firmado es inmutable.** Si hay un error se emite una nueva versión y la anterior queda anulada, nunca se reemplaza.

## 7. Glosario

| Sigla             | Significado                                                                                                       |
| ----------------- | ----------------------------------------------------------------------------------------------------------------- |
| EBR / EBA / EBE   | Educación Básica Regular / Alternativa / Especial                                                                 |
| ETP               | Educación Técnico-Productiva                                                                                      |
| CEBA / CEBE       | Centro de Educación Básica Alternativa / Especial                                                                 |
| PRITE             | Programa de Intervención Temprana (0 a 3 años)                                                                    |
| SAANEE            | Servicio de Apoyo y Asesoramiento para la Atención de NEE                                                         |
| NEE / POI         | Necesidades educativas especiales / Plan de Orientación Individual                                                |
| CETPRO            | Centro de Educación Técnico-Productiva                                                                            |
| IEST / IES / EESP | Institutos y escuelas de educación superior                                                                       |
| CNEB              | Currículo Nacional de la Educación Básica                                                                         |
| SIAGIE            | Sistema de Información de Apoyo a la Gestión de la IE (MINEDU)                                                    |
| Firma Perú        | Plataforma de firma digital del Estado peruano (PCM), con un agente que se instala en la computadora del firmante |
| PAdES             | Formato de firma digital avanzada incrustada en archivos PDF                                                      |
| SaaS              | Software como servicio                                                                                            |
| RLS               | Row Level Security de PostgreSQL                                                                                  |
