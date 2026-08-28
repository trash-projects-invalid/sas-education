# Escenarios de atributos de calidad de Stella Education

## EQ-01. Seguridad: aislamiento entre instituciones

| Elemento                | Descripción                                                                                                                                                    |
| ----------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Seguridad y aislamiento multiinstitución                                                                                                                       |
| **Fuente**              | Usuario autenticado de la institución A                                                                                                                        |
| **Estímulo**            | Intenta consultar o modificar un estudiante, una nota o un documento de la institución B (cambiando el identificador en la URL o en el cuerpo de la petición). |
| **Condición**           | Operación normal; RLS activo en todas las bases de datos.                                                                                                      |
| **Respuesta esperada**  | La petición no devuelve datos de B ni ejecuta cambios. Se registra el intento en la auditoría.                                                                 |
| **Medida**              | 100 % de los intentos de acceso entre instituciones de la batería de pruebas son rechazados, sin datos de otra institución en las respuestas.                  |
| **Verificación**        | Pruebas automatizadas de acceso cruzado por servicio y prueba directa de RLS en la base de datos.                                                              |
| **Estado**              | Pendiente de construir la batería de pruebas de acceso cruzado.                                                                                                |

## EQ-02. Adaptabilidad: alta de un tipo de institución

| Elemento                | Descripción                                                                                                                       |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Adaptabilidad / configurabilidad                                                                                                  |
| **Fuente**              | Superadmin de plataforma                                                                                                          |
| **Estímulo**            | Se necesita atender un tipo nuevo de institución (por ejemplo, un centro de idiomas) o desactivar el aula virtual en un CEBE.     |
| **Condición**           | Los módulos genéricos ya están desplegados.                                                                                       |
| **Respuesta esperada**  | Se crea la plantilla o se cambian las funciones y la interfaz se adapta (menú, etiquetas, pantallas) sin cambiar código.          |
| **Medida**              | Cero despliegues necesarios; el cambio de funciones se refleja en un máximo de 60 segundos.                                       |
| **Verificación**        | Crear una plantilla de prueba, asignarla a un servicio y comprobar menú, etiquetas y bloqueo de rutas desactivadas en el gateway. |
| **Estado**              | Pendiente de definir el catálogo inicial de plantillas.                                                                           |

## EQ-03. Escalabilidad: emisión masiva de libretas al cierre de bimestre

| Elemento                | Descripción                                                                                                                                                                  |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Escalabilidad                                                                                                                                                                |
| **Fuente**              | Secretarías de varias instituciones                                                                                                                                          |
| **Estímulo**            | Al cerrar el bimestre se solicitan libretas para miles de estudiantes al mismo tiempo.                                                                                       |
| **Condición**           | Workers de PDF desplegados con escalado horizontal según el retraso (_lag_) del tópico de trabajos.                                                                          |
| **Respuesta esperada**  | Los trabajos se encolan, se procesan en paralelo y el usuario ve el avance; el resto de módulos no se degrada.                                                               |
| **Medida**              | **Propuesta por validar:** 5 000 libretas generadas en menos de 15 minutos con 4 réplicas del worker; el tiempo de respuesta de los demás módulos no aumenta más de un 10 %. |
| **Verificación**        | Prueba de carga encolando 5 000 trabajos y midiendo el tiempo total, el _lag_ de Kafka y la latencia de los demás servicios.                                                 |
| **Estado**              | Pendiente de fijar el tamaño de las plantillas y la infraestructura de prueba.                                                                                               |

## EQ-04. Rendimiento: consultas del portal y registro de notas

| Elemento                | Descripción                                                                                                                                                 |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Rendimiento                                                                                                                                                 |
| **Fuente**              | Apoderados, estudiantes y docentes                                                                                                                          |
| **Estímulo**            | Consultan notas y asistencia o registran calificaciones de una sección.                                                                                     |
| **Condición**           | Operación normal con datos representativos.                                                                                                                 |
| **Respuesta esperada**  | La respuesta es rápida y sin errores.                                                                                                                       |
| **Medida**              | **Propuesta por validar:** el 95 % de las consultas responde en 500 ms o menos en el gateway, con menos del 1 % de errores, con 1 000 usuarios simultáneos. |
| **Verificación**        | Pruebas de carga sobre el gateway registrando percentiles de latencia y tasa de errores.                                                                    |
| **Estado**              | Pendiente de definir el entorno de prueba.                                                                                                                  |

## EQ-05. Disponibilidad: falla de un servicio no crítico

| Elemento                | Descripción                                                                                                                           |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Disponibilidad y resiliencia                                                                                                          |
| **Fuente**              | Falla interna                                                                                                                         |
| **Estímulo**            | El servicio de aula virtual o los workers de PDF dejan de responder.                                                                  |
| **Condición**           | Sistema en producción con monitoreo.                                                                                                  |
| **Respuesta esperada**  | Matrícula, notas, asistencia y pagos siguen operando. Los trabajos de PDF quedan en Kafka y se procesan cuando el worker se recupera. |
| **Medida**              | **Propuesta por validar:** disponibilidad mensual de 99,5 % para los módulos críticos; cero trabajos de PDF perdidos.                 |
| **Verificación**        | Pruebas de caos (detener servicios no críticos) y revisión de los registros de monitoreo.                                             |
| **Estado**              | Pendiente de definir los servicios críticos y la ventana de mantenimiento.                                                            |

## EQ-06. Integridad: documento firmado alterado

| Elemento                | Descripción                                                                                                                 |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Integridad y no repudio                                                                                                     |
| **Fuente**              | Verificador externo                                                                                                         |
| **Estímulo**            | Recibe un certificado firmado que alguien modificó después de la firma y escanea su QR.                                     |
| **Condición**           | El documento original está registrado con su hash y su firma PAdES.                                                         |
| **Respuesta esperada**  | La página de verificación muestra el documento original; el visor de PDF marca la firma como inválida en la copia alterada. |
| **Medida**              | 100 % de las alteraciones de prueba detectadas; cada documento firmado tiene firmante, fecha y hash registrados.            |
| **Verificación**        | Alterar copias firmadas de prueba y validarlas con la página de verificación y con un validador de firmas PAdES.            |
| **Estado**              | Pendiente de confirmar la versión de la especificación técnica de Firma Perú.                                               |

## EQ-07. Mantenibilidad: cambio de proveedor externo

| Elemento                | Descripción                                                                                                             |
| ----------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Mantenibilidad e interoperabilidad                                                                                      |
| **Fuente**              | Equipo de desarrollo                                                                                                    |
| **Estímulo**            | Se cambia la pasarela de pagos o el OSE de SUNAT.                                                                       |
| **Condición**           | Cada proveedor está detrás de un puerto del dominio.                                                                    |
| **Respuesta esperada**  | Solo se crea un nuevo adaptador en el servicio Finanzas; no cambian los casos de uso ni otros servicios.                |
| **Medida**              | Cambios limitados a la carpeta `adapter/outbound` del servicio y a su configuración; las pruebas del servicio aprueban. |
| **Verificación**        | Revisión de los cambios y ejecución de las pruebas de contrato del puerto.                                              |
| **Estado**              | Pendiente de elegir los proveedores iniciales.                                                                          |

## EQ-08. Observabilidad: rastrear una emisión de certificado

| Elemento                | Descripción                                                                                                               |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **Atributo de calidad** | Observabilidad                                                                                                            |
| **Fuente**              | Soporte técnico                                                                                                           |
| **Estímulo**            | Un director reporta que un certificado no se generó.                                                                      |
| **Condición**           | Trazas distribuidas y registros estructurados activos.                                                                    |
| **Respuesta esperada**  | Con el identificador de la solicitud se ve el recorrido completo: gateway → Documentos → Kafka → worker → almacenamiento. |
| **Medida**              | El paso que falló se identifica en menos de 10 minutos.                                                                   |
| **Verificación**        | Simulacro de incidente con un trabajo fallido.                                                                            |
| **Estado**              | Pendiente de elegir la herramienta de trazas.                                                                             |

## Resumen de escenarios

| Código | Atributo de calidad | Qué se busca comprobar                                                |
| ------ | ------------------- | --------------------------------------------------------------------- |
| EQ-01  | Seguridad           | Que ninguna institución acceda a datos de otra.                       |
| EQ-02  | Adaptabilidad       | Que los módulos se adecuen al tipo de institución sin programar.      |
| EQ-03  | Escalabilidad       | Que la emisión masiva de documentos no degrade el sistema.            |
| EQ-04  | Rendimiento         | Que las consultas frecuentes respondan dentro del tiempo establecido. |
| EQ-05  | Disponibilidad      | Que los módulos críticos sigan operando ante fallas de otros.         |
| EQ-06  | Integridad          | Que los documentos firmados no puedan alterarse sin detectarse.       |
| EQ-07  | Mantenibilidad      | Que un proveedor externo se cambie sin tocar el negocio.              |
| EQ-08  | Observabilidad      | Que una falla pueda rastrearse entre servicios.                       |
