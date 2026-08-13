# Requisitos funcionales

## Requisitos funcionales

### Plataforma y organización

| ID   | Requisito funcional                                                                                                                                                 |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RF01 | El sistema debe permitir registrar organizaciones (clientes SaaS) con su RUC, marca, plan de suscripción y subdominio.                                              |
| RF02 | El sistema debe permitir crear servicios educativos dentro de una organización, indicando tipo de servicio, modalidad, código modular (si es regulado) y plantilla. |
| RF03 | El sistema debe permitir registrar sedes y periféricos de cada organización.                                                                                        |
| RF04 | El sistema debe permitir administrar plantillas por tipo de institución con sus funciones por defecto, etiquetas de interfaz y esquema de calificación.             |
| RF05 | El sistema debe permitir activar y desactivar funciones por servicio educativo, dentro de lo que permite el plan contratado.                                        |
| RF06 | El sistema debe mostrar en la interfaz solo los módulos activos y con las etiquetas de la plantilla del servicio seleccionado.                                      |

### Identidad y acceso

| ID   | Requisito funcional                                                                                                      |
| ---- | ------------------------------------------------------------------------------------------------------------------------ |
| RF07 | El sistema debe permitir iniciar y cerrar sesión, con segundo factor de autenticación opcional.                          |
| RF08 | El sistema debe permitir que una misma cuenta tenga roles en varias organizaciones y servicios, y cambiar entre ellos.   |
| RF09 | El sistema debe permitir gestionar roles y permisos por organización.                                                    |
| RF10 | El sistema debe registrar en una bitácora de auditoría las operaciones sobre datos académicos, financieros y documentos. |

### Estructura y gestión académica

| ID   | Requisito funcional                                                                                                            |
| ---- | ------------------------------------------------------------------------------------------------------------------------------ |
| RF11 | El sistema debe ofrecer el catálogo curricular regulado (MINEDU) de solo lectura para las modalidades EBR, EBA y EBE.          |
| RF12 | El sistema debe permitir que cada institución defina programas propios con etapas, niveles, cursos, créditos y prerrequisitos. |
| RF13 | El sistema debe permitir crear periodos (año escolar, periodo promocional, semestre, módulo o ciclo comercial) y sus cortes.   |
| RF14 | El sistema debe permitir crear secciones y aulas, asignar docentes y armar horarios.                                           |

### Personas, admisión y matrícula

| ID   | Requisito funcional                                                                                                                   |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------- |
| RF15 | El sistema debe permitir registrar estudiantes, apoderados y personal, validando el DNI con RENIEC cuando la integración esté activa. |
| RF16 | El sistema debe permitir gestionar procesos de admisión y pruebas de ubicación.                                                       |
| RF17 | El sistema debe permitir matricular por sección y, si la función `course_enrollment` está activa, por curso.                          |
| RF18 | El sistema debe permitir registrar traslados, convalidaciones, revalidaciones y retiros como cambios de estado.                       |

### Evaluación y asistencia

| ID   | Requisito funcional                                                                                                                       |
| ---- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| RF19 | El sistema debe permitir registrar calificaciones según el esquema del servicio: competencias, descriptivo, numérico ponderado o puntaje. |
| RF20 | El sistema debe permitir cerrar cortes (bloqueando sus notas) y cerrar periodos (generando la situación final).                           |
| RF21 | El sistema debe permitir registrar la asistencia de estudiantes y personal de forma manual o con código QR.                               |
| RF22 | El sistema debe permitir programar simulacros, registrar resultados y calcular rankings, cuando la función esté activa.                   |

### Documentos y firma digital

| ID   | Requisito funcional                                                                                                                                                              |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RF23 | El sistema debe generar en PDF libretas, constancias, actas, certificados y comprobantes, a partir de plantillas de documento por tipo de servicio.                              |
| RF24 | El sistema debe permitir solicitar la generación de documentos de forma individual o por lotes (por ejemplo, todas las libretas de una sección) y mostrar el avance del proceso. |
| RF25 | El sistema debe permitir que el firmante autorizado firme los documentos con el **agente Firma Perú**, usando su propio certificado digital, en formato PAdES.                   |
| RF26 | El sistema debe guardar el PDF firmado como versión final inmutable y registrar quién firmó, cuándo y el hash del archivo.                                                       |
| RF27 | El sistema debe incluir en cada documento un código QR con un enlace de verificación pública que muestre si el documento es auténtico, vigente o anulado.                        |
| RF28 | El sistema debe permitir anular un documento firmado y emitir una nueva versión, conservando el historial.                                                                       |

### Finanzas

| ID   | Requisito funcional                                                                                         |
| ---- | ----------------------------------------------------------------------------------------------------------- |
| RF29 | El sistema debe permitir definir conceptos de pago, cronogramas, descuentos y becas por servicio y periodo. |
| RF30 | El sistema debe permitir registrar cobros en caja y pagos en línea mediante la pasarela de pagos.           |
| RF31 | El sistema debe emitir comprobantes electrónicos y enviarlos a SUNAT a través de un OSE.                    |

### Comunicación, bienestar e integraciones

| ID   | Requisito funcional                                                                                                                            |
| ---- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| RF32 | El sistema debe permitir enviar comunicados y notificaciones por la plataforma, correo y WhatsApp.                                             |
| RF33 | El sistema debe ofrecer un portal para estudiantes y apoderados con notas, asistencia, pagos, comunicados y documentos.                        |
| RF34 | El sistema debe generar alertas de riesgo por inasistencia, bajo rendimiento o deuda.                                                          |
| RF35 | El sistema debe permitir registrar el perfil NEE, el POI, las sesiones PRITE y las visitas SAANEE, cuando la función de inclusión esté activa. |
| RF36 | El sistema debe permitir publicar recursos, tareas y foros en un aula virtual, cuando la función esté activa.                                  |
| RF37 | El sistema debe exportar nóminas, notas y actas en el formato SIAGIE de cada modalidad.                                                        |

---

## Relación entre historias de usuario y requisitos funcionales

| Historia de usuario                                   | Requisitos funcionales relacionados |
| ----------------------------------------------------- | ----------------------------------- |
| HU01 Registrar organización y servicios con plantilla | RF01, RF02, RF03, RF04              |
| HU02 Activar funciones por servicio                   | RF05, RF06                          |
| HU03 Sesión única y cambio de institución             | RF07, RF08, RF09                    |
| HU04 Configurar programas, periodos y secciones       | RF11, RF12, RF13, RF14              |
| HU05 Registrar personas con DNI                       | RF15                                |
| HU06 Matricular                                       | RF16, RF17, RF18                    |
| HU07 Carga docente y horarios                         | RF14                                |
| HU08 Registrar asistencia                             | RF21                                |
| HU09 Registrar calificaciones                         | RF19                                |
| HU10 Cerrar corte o periodo                           | RF20, RF10                          |
| HU11 Generar documentos PDF                           | RF23, RF24                          |
| HU12 Firmar con Firma Perú                            | RF25, RF26, RF28                    |
| HU13 Verificar documento                              | RF27                                |
| HU14 Cronogramas y cobros                             | RF29, RF30, RF31                    |
| HU15 Portal del apoderado                             | RF30, RF33                          |
| HU16 Simulacros y ranking                             | RF22                                |
| HU17 Inclusión NEE                                    | RF35                                |
| HU18 Comunicados y alertas                            | RF32, RF34                          |
| HU19 Exportar a SIAGIE                                | RF37                                |
| HU20 Aula virtual                                     | RF36                                |
