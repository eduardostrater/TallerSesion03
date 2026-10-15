# SPEC-F06: Carga del día

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F06-1](../HU-F06-1_consolidar-carga-del-dia.md), [HU-F06-2](../HU-F06-2_comparar-programado-vs-real.md) |
| Prioridad WSJF | #6 (5.3); se construye después de F05 por dependencia |
| Incremento | MVP 2: Blending |
| Depende de | F05 |
| Habilita | F08, F10 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Consolidar automáticamente la carga diaria a planta y su ley de cabeza, cerrar el día operativo y medir el cumplimiento del programa de blending.

## 2. Alcance

**Incluye:** consolidado por tolva y total, ley de cabeza, cierre del día, comparativo programado vs. real.
**No incluye:** reapertura de días cerrados (fuera del MVP).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Supervisor de planta | Consulta y cierra el día. |
| Jefe de planta | Consulta el cumplimiento. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F06-01 | El día de operación se crea en estado `ABI` al registrar su primer programa o envío. |
| RN-F06-02 | Carga del día = Σ toneladas de los envíos no anulados de la fecha. |
| RN-F06-03 | Ley de cabeza = Σ(t × ley del envío) / Σ t de los envíos no anulados. |
| RN-F06-04 | Al cerrar el día se fijan el tonelaje total, la ley de cabeza, la fecha y el usuario de cierre; luego no se admiten envíos, anulaciones ni cambios de programa. |
| RN-F06-05 | Desviación % = (real − programado) / programado × 100, por pila y tolva. Si no hubo programa, se muestra "No programado". |
| RN-F06-06 | Se resaltan desviaciones mayores al umbral `MPB_UMBRAL_DESVIACION_PCT` (valor inicial 10 %). |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F06-01 | Mostrar por fecha el tonelaje por tolva, el total y la ley de cabeza. | HU-F06-1 / CA1 |
| RF-F06-02 | Cerrar el día de operación. | HU-F06-1 / CA2 |
| RF-F06-03 | Mostrar programado, real y desviación % por pila y tolva. | HU-F06-2 / CA1 |
| RF-F06-04 | Resaltar las desviaciones que superan el umbral. | HU-F06-2 |

## 6. Modelo de datos

**`planta.MPB_CARGA_DIA`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `DFECHA_OPERACION` | DATE | No | PK. |
| `CDIA_ESTADO` | CHAR(3) | No | `ABI` o `CER`. |
| `NTONELAJE_CANTIDAD` | DECIMAL(14,3) | Sí | Se fija al cerrar. |
| `NLEY_AU` | DECIMAL(10,4) | Sí | Ley de cabeza al cerrar. |
| `DFECHA_CIERRE` | DATETIME2 | Sí | |
| `CUSUARIO_CIERRE` | VARCHAR(50) | Sí | |
| Auditoría | — | — | |

**Vistas:** `planta.VW_MPB_CARGA_DIA_TOLVA` (fecha, tolva, t, ley) y `planta.VW_MPB_BLENDING_CUMPLIMIENTO` (fecha, pila, tolva, programado, real, desviación).

**Procedimiento:** `planta.USP_MPB_CIERRA_DIA (@pi_FechaOperacion, @pi_Usuario, @po_Retorno, @po_Mensaje)`.

**Parámetro:** `MPB_UMBRAL_DESVIACION_PCT`.

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| GET | `/api/carga-dia/{fecha}` | Consolidado por tolva y total. | — |
| POST | `/api/carga-dia/{fecha}/cerrar` | Cierra el día. | `DIA_CERRADO`, `DIA_SIN_ENVIOS` |
| GET | `/api/blending/cumplimiento/{fecha}` | Programado vs. real. | — |

Ejemplo `GET /api/carga-dia/2026-10-20`:

```json
{ "fecha": "2026-10-20", "estado": "ABI", "tonelaje": 1840.500, "leyCabeza": 6.4372,
  "tolvas": [ { "codigo": "T1", "tonelaje": 920.250, "leyAu": 6.2010 },
              { "codigo": "T2", "tonelaje": 920.250, "leyAu": 6.6734 } ] }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `CargaDia.razor` | Selector de fecha, tarjetas de total y ley de cabeza, tabla por tolva, estado del día y botón Cerrar día con confirmación. |
| `Cumplimiento.razor` | Tabla pila × tolva con programado, real y desviación; resaltado por umbral; totales por tolva. |

## 9. Permisos

| Acción | Supervisor | Jefe de planta | Op. despacho |
|--------|:---:|:---:|:---:|
| Consultar carga del día | ✔ | ✔ | ✔ |
| Cerrar día | ✔ | | |
| Consultar cumplimiento | ✔ | ✔ | |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F06-01 | Día con envíos de 100 t × 6 g/t (T1) y 300 t × 7 g/t (T2). | Total 400 t; ley de cabeza 6.7500 g/t. | HU-F06-1 / CA1 |
| CP-F06-02 | Envío anulado en la fecha. | No suma al total ni a la ley. | RN-F06-02 |
| CP-F06-03 | Cerrar el día y luego registrar un envío. | Cierre OK; el envío recibe 409 `DIA_CERRADO`. | HU-F06-1 / CA2 |
| CP-F06-04 | Programado 100 t, real 85 t. | Desviación −15 %, resaltada. | HU-F06-2 / CA1 |
| CP-F06-05 | Envío sin programa. | Se muestra "No programado". | RN-F06-05 |

## 11. Preguntas abiertas

1. ¿El día de operación coincide con el día calendario o va de turno a turno (por ejemplo, 07:00 a 06:59)?
2. ¿Quién puede cerrar el día y hasta qué hora?
3. ¿El umbral de desviación es único o por tolva?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
