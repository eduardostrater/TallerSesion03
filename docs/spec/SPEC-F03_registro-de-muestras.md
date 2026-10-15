# SPEC-F03: Registro de muestra por volquete

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F03-1](../HU-F03-1_generar-codigo-de-muestra-del-volquete.md), [HU-F03-2](../HU-F03-2_registrar-ley-de-la-muestra.md) |
| Prioridad WSJF | #3 (11.3) |
| Incremento | MVP 1: Ley de pila |
| Depende de | F02 |
| Habilita | F04, F07 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Asociar a cada volquete una muestra con código único y trazable, y registrar su ley una sola vez para que alimente el cálculo de la ley de la pila.

## 2. Alcance

**Incluye:** generación del código de muestra, impresión de etiqueta, registro y corrección manual de la ley.
**No incluye:** recepción automática de leyes (SPEC-F07); cálculo de la ley de pila (SPEC-F04).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Muestrero | Registra la toma de muestra e imprime la etiqueta. |
| Analista de laboratorio | Registra y corrige la ley. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F03-01 | Relación 1:1 entre volquete y muestra; un volquete anulado no admite muestra. |
| RN-F03-02 | El código de muestra lo genera el sistema: `M-<AAAAMMDD>-<correlativo de 5 dígitos por día>`. |
| RN-F03-03 | La muestra nace en estado `PEN` (Pendiente de ley). |
| RN-F03-04 | La ley se registra en Au g/t, con 4 decimales, y debe ser mayor o igual a 0. |
| RN-F03-05 | Al registrar la ley, la muestra pasa a `LEY` y se dispara el recálculo de la ley de la pila (RN-F04-01). |
| RN-F03-06 | Una ley ya registrada solo se corrige con motivo, mientras la pila esté `ABI`, y queda auditada. |
| RN-F03-07 | La fecha de resultado no puede ser anterior a la fecha de toma. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F03-01 | Registrar la toma de muestra de un volquete y generar su código. | HU-F03-1 / CA1, CA3 |
| RF-F03-02 | Rechazar una segunda muestra para el mismo volquete. | HU-F03-1 / CA2 |
| RF-F03-03 | Imprimir la etiqueta con código de barras (Code 128), código de muestra, pila y fecha. | HU-F03-1 |
| RF-F03-04 | Buscar una muestra por código (teclado o lector de código de barras). | HU-F03-2 / CA3 |
| RF-F03-05 | Registrar la ley de una muestra `PEN`. | HU-F03-2 / CA1 |
| RF-F03-06 | Rechazar valores negativos o no numéricos. | HU-F03-2 / CA2 |
| RF-F03-07 | Corregir una ley registrada con motivo obligatorio. | RN-F03-06 |

## 6. Modelo de datos

**`planta.MPB_MUESTRA`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NMUESTRA_ID` | INT IDENTITY | No | PK `PK_MPB_MUESTRA`. |
| `CMUESTRA_CODIGO` | VARCHAR(20) | No | Único (`IX_UX_MPB_MUESTRA_01`). |
| `NVOLQUETE_ID` | INT | No | FK `FK_MPB_MUESTRA_MPB_VOLQUETE`; único (`IX_UX_MPB_MUESTRA_02`). |
| `CMUESTRA_ESTADO` | CHAR(3) | No | `PEN` o `LEY`. |
| `NLEY_AU` | DECIMAL(10,4) | Sí | Ley en g/t. |
| `DFECHA_TOMA` | DATETIME2 | No | |
| `CUSUARIO_TOMA` | VARCHAR(50) | No | Muestrero. |
| `DFECHA_RESULTADO` | DATETIME2 | Sí | |
| `CUSUARIO_RESULTADO` | VARCHAR(50) | Sí | Analista. |
| Auditoría | — | — | Columnas comunes. |

**Procedimientos:** `planta.USP_MPB_INS_MUESTRA`, `planta.USP_MPB_REGISTRA_LEY` (actualiza la muestra y llama al recálculo de la pila en la misma transacción), `planta.USP_MPB_CORRIGE_LEY`.

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| POST | `/api/volquetes/{id}/muestra` | Registra la toma y genera el código. | `MUESTRA_EXISTENTE`, `VOLQUETE_ANULADO` |
| GET | `/api/muestras/{codigo}` | Busca muestra por código. | `MUESTRA_INEXISTENTE` |
| GET | `/api/muestras/{codigo}/etiqueta` | Devuelve la etiqueta para imprimir. | `MUESTRA_INEXISTENTE` |
| PUT | `/api/muestras/{codigo}/ley` | Registra la ley. | `MUESTRA_CON_LEY`, `LEY_INVALIDA`, `FECHA_RESULTADO_INVALIDA` |
| PUT | `/api/muestras/{codigo}/ley/correccion` | Corrige la ley. | `PILA_NO_ABIERTA`, `MOTIVO_REQUERIDO`, `LEY_INVALIDA` |

Ejemplo `PUT /api/muestras/M-20261015-00042/ley`:

```json
// Solicitud
{ "leyAu": 7.2150, "fechaResultado": "2026-10-16T11:20:00" }

// Respuesta 200
{ "codigo": "M-20261015-00042", "estado": "LEY",
  "pila": { "codigo": "PIL-TJ01-20261015-001", "leyAu": 6.9821, "coberturaPct": 66.7 } }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `RegistroMuestra.razor` | Desde la grilla de volquetes: botón Registrar muestra, muestra el código generado y abre la vista de impresión de la etiqueta. |
| `RegistroLey.razor` | Campo de búsqueda con foco automático (compatible con lector), datos del volquete y la pila, campo de ley y fecha de resultado. Lista de muestras pendientes del día. |

## 9. Permisos

| Acción | Muestrero | Analista lab. | Supervisor |
|--------|:---:|:---:|:---:|
| Registrar toma de muestra | ✔ | | ✔ |
| Registrar ley | | ✔ | |
| Corregir ley | | ✔ | |
| Consultar muestras | ✔ | ✔ | ✔ |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F03-01 | Registrar muestra de un volquete sin muestra. | 201; código único, estado `PEN`. | HU-F03-1 / CA1, CA3 |
| CP-F03-02 | Registrar una segunda muestra del mismo volquete. | 409 `MUESTRA_EXISTENTE`. | HU-F03-1 / CA2 |
| CP-F03-03 | Registrar ley 7.215 en muestra `PEN`. | 200; estado `LEY`; ley de pila recalculada. | HU-F03-2 / CA1 |
| CP-F03-04 | Registrar ley −1 y "abc". | 422 `LEY_INVALIDA`. | HU-F03-2 / CA2 |
| CP-F03-05 | Buscar código inexistente. | 404 `MUESTRA_INEXISTENTE`. | HU-F03-2 / CA3 |
| CP-F03-06 | Corregir ley con pila `CER`. | 409 `PILA_NO_ABIERTA`. | RN-F03-06 |
| CP-F03-07 | Imprimir etiqueta y leerla con lector. | El lector devuelve el código exacto. | RF-F03-03 |

## 11. Preguntas abiertas

1. ¿Qué elementos además de Au se analizan (Ag, Cu, humedad)? Afecta el modelo de datos de la ley.
2. ¿Qué formato e impresora se usa para las etiquetas?
3. ¿Existen muestras de control (duplicados, blancos, estándares) que deban registrarse?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
