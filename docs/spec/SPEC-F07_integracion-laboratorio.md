# SPEC-F07: Integración con laboratorio

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F07-1](../HU-F07-1_recibir-leyes-automaticamente.md), [HU-F07-2](../HU-F07-2_gestionar-errores-de-integracion.md) |
| Prioridad WSJF | #9 (2.4) |
| Incremento | Evolución |
| Depende de | F03 |
| Versión / Estado | 0.1 / Borrador (requiere definir la interfaz del laboratorio) |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Recibir automáticamente las leyes emitidas por el sistema de laboratorio y gestionar los resultados que no se puedan aplicar, eliminando la digitación manual.

## 2. Alcance

**Incluye:** proceso de carga periódica, aplicación idempotente de resultados, bandeja de errores y reproceso.
**No incluye:** cambios en el sistema de laboratorio; el registro manual de SPEC-F03 se mantiene como contingencia.

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Sistema de laboratorio | Emite los resultados. |
| Analista de laboratorio | Revisa la bandeja de errores. |
| Administrador | Reprocesa y monitorea la integración. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F07-01 | El resultado se asocia a la muestra por `CMUESTRA_CODIGO`. |
| RN-F07-02 | Cada resultado tiene un identificador externo; si ya fue aplicado, se ignora (idempotencia). |
| RN-F07-03 | Un resultado válido se aplica con la misma lógica que el registro manual (`USP_MPB_REGISTRA_LEY`). |
| RN-F07-04 | Si la muestra ya tiene ley y el valor es distinto, el resultado va a la bandeja como `LEY_DISTINTA` (no se sobrescribe). |
| RN-F07-05 | Motivos de rechazo: `MUESTRA_INEXISTENTE`, `LEY_INVALIDA`, `LEY_DISTINTA`, `PILA_NO_ABIERTA`. |
| RN-F07-06 | Un rechazo corregido se puede reprocesar; si se aplica, pasa a estado `REP`. |
| RN-F07-07 | Cada ejecución se registra en la tabla de control de procesos con inicio, fin, estado, procesados y errores. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F07-01 | Cargar periódicamente (cada 15 minutos, configurable) los resultados nuevos. | HU-F07-1 / CA1 |
| RF-F07-02 | No duplicar resultados ya aplicados. | HU-F07-1 / CA2 |
| RF-F07-03 | Enviar a la bandeja los resultados no aplicables con su motivo. | HU-F07-2 / CA1 |
| RF-F07-04 | Corregir el código de muestra o el valor y reprocesar. | HU-F07-2 / CA2 |
| RF-F07-05 | Consultar el historial de ejecuciones del proceso. | RN-F07-07 |

## 6. Modelo de datos

**`etl.MPB_LAB_RESULTADO_LOG`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NRESULTADO_LOG_ID` | INT IDENTITY | No | PK. |
| `CRESULTADO_EXTERNO_ID` | VARCHAR(50) | No | Único. |
| `CMUESTRA_CODIGO` | VARCHAR(20) | No | Como llegó del laboratorio. |
| `NLEY_AU` | DECIMAL(10,4) | Sí | |
| `DFECHA_EMISION` | DATETIME2 | No | |
| `CRESULTADO_ESTADO` | CHAR(3) | No | `APL` (aplicado), `ERR` (en bandeja), `REP` (reprocesado). |
| `CMOTIVO_CODIGO` | VARCHAR(30) | Sí | RN-F07-05. |
| `NPROCESO_ID` | INT | No | FK a la ejecución. |
| Auditoría | — | — | |

**`etl.MPB_PROCESO_CONTROL`**: `NPROCESO_ID`, `DFECHA_INICIO`, `DFECHA_FIN`, `CPROCESO_ESTADO`, `NPROCESADOS_CANTIDAD`, `NERRORES_CANTIDAD`, `VMENSAJE_DESCRIPCION`, `CUSUARIO_CREACION`, `GEJECUCION_ID`.

**Procedimiento:** `etl.USP_MPB_CARGA_LIMS`. Lee el origen a través de un synonym o una vista de integración (nunca nombres de servidor dentro del código de negocio).

**Ejecución:** job de SQL Agent o servicio programado (por definir según la interfaz del laboratorio).

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| GET | `/api/integracion/laboratorio/errores?motivo=&pagina=` | Bandeja de errores. | — |
| PUT | `/api/integracion/laboratorio/errores/{id}` | Corrige código o valor. | `RESULTADO_YA_APLICADO` |
| POST | `/api/integracion/laboratorio/errores/{id}/reprocesar` | Reprocesa un resultado. | Mismos motivos de RN-F07-05 |
| GET | `/api/integracion/laboratorio/ejecuciones` | Historial de ejecuciones. | — |

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `IntegracionLaboratorio.razor` | Pestaña Bandeja (errores con motivo, edición y Reprocesar) y pestaña Ejecuciones (historial con contadores y estado). |

## 9. Permisos

Ventana `MPB_INTEGRACION`: `CON` para Analista de laboratorio y Administrador; `MOD` (corregir y reprocesar) para Administrador.

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F07-01 | Resultado válido para muestra `PEN`. | Muestra en `LEY`; ley de pila recalculada; log `APL`. | HU-F07-1 / CA1 |
| CP-F07-02 | El mismo resultado llega dos veces. | Se aplica una sola vez. | HU-F07-1 / CA2 |
| CP-F07-03 | Código de muestra inexistente. | Log `ERR` con `MUESTRA_INEXISTENTE`. | HU-F07-2 / CA1 |
| CP-F07-04 | Corregir el código y reprocesar. | Se aplica; log `REP`. | HU-F07-2 / CA2 |
| CP-F07-05 | Muestra con ley distinta a la recibida. | Log `ERR` con `LEY_DISTINTA`; la ley no cambia. | RN-F07-04 |
| CP-F07-06 | Caída del origen durante la ejecución. | Ejecución registrada con estado de error; sin cambios parciales. | RN-F07-07 |

## 11. Preguntas abiertas

1. ¿Qué sistema de laboratorio se usa y qué interfaz ofrece (API, archivo, vista de base de datos)?
2. ¿Con qué frecuencia emite resultados el laboratorio?
3. ¿Se deben recibir otros elementos además de Au?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
