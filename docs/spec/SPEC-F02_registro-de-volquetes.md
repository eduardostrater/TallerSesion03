# SPEC-F02: Registro de volquetes

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F02-1](../HU-F02-1_registrar-volquete-en-una-pila.md), [HU-F02-2](../HU-F02-2_consultar-y-corregir-volquetes-del-turno.md) |
| Prioridad WSJF | #2 (13.0) |
| Incremento | MVP 1: Ley de pila |
| Depende de | F01 |
| Habilita | F03, F04 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Registrar cada volquete que ingresa a una pila, con su tonelaje, para conocer el tonelaje real de la pila y servir de base para la ponderación de la ley.

## 2. Alcance

**Incluye:** registro de volquetes en balanza; consulta y corrección de los volquetes del turno.
**No incluye:** integración automática con la balanza (posible evolución); registro de la muestra (SPEC-F03).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Operador de balanza | Registra volquetes. |
| Supervisor de planta | Consulta y corrige volquetes del turno. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F02-01 | Solo se asignan volquetes a pilas en estado `ABI`. |
| RN-F02-02 | El tonelaje es mayor a 0 y menor o igual al máximo configurado (parámetro `MPB_TONELAJE_MAX_VOLQUETE`, valor inicial 40 t). |
| RN-F02-03 | La fecha y hora de pesaje no puede ser futura ni anterior a la fecha de creación de la pila. |
| RN-F02-04 | El turno se deriva de la hora de pesaje: Día (07:00–18:59) y Noche (19:00–06:59). |
| RN-F02-05 | Cada alta suma su tonelaje a `NTONELAJE_CANTIDAD` y a `NSTOCK_CANTIDAD` de la pila, y suma 1 a `NVOLQUETE_CANTIDAD`, en la misma transacción. |
| RN-F02-06 | Solo se corrigen volquetes de pilas `ABI`; la corrección exige motivo y recalcula los acumulados de la pila. |
| RN-F02-07 | Si el volquete ya tiene ley, la corrección de tonelaje recalcula también la ley ponderada de la pila (SPEC-F04). |
| RN-F02-08 | Un volquete no se elimina; si fue un error, se anula con motivo y se descuenta de los acumulados. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F02-01 | Registrar volquete con placa, tonelaje, fecha/hora de pesaje y pila. | HU-F02-1 / CA1 |
| RF-F02-02 | Mostrar el acumulado de la pila después de grabar. | HU-F02-1 / CA1 |
| RF-F02-03 | Rechazar volquetes para pilas cerradas o consumidas. | HU-F02-1 / CA2 |
| RF-F02-04 | Rechazar tonelajes fuera de rango con mensaje de validación. | HU-F02-1 / CA3 |
| RF-F02-05 | Listar volquetes por fecha, turno y pila, con el estado de su muestra. | HU-F02-2 / CA1 |
| RF-F02-06 | Corregir el tonelaje o la placa de un volquete con motivo obligatorio y auditoría. | HU-F02-2 / CA2 |
| RF-F02-07 | Anular un volquete con motivo obligatorio. | RN-F02-08 |

## 6. Modelo de datos

**`planta.MPB_VOLQUETE`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NVOLQUETE_ID` | INT IDENTITY | No | PK `PK_MPB_VOLQUETE`. |
| `NPILA_ID` | INT | No | FK `FK_MPB_VOLQUETE_MPB_PILA`. |
| `CPLACA_CODIGO` | VARCHAR(10) | No | Placa del volquete. |
| `NTONELAJE_CANTIDAD` | DECIMAL(10,3) | No | Tonelaje neto. |
| `DFECHA_PESAJE` | DATETIME2 | No | |
| `CTURNO_CODIGO` | CHAR(1) | No | `D` o `N` (RN-F02-04). |
| `BANULADO` | BIT | No | Default 0. |
| `VANULACION_MOTIVO` | NVARCHAR(200) | Sí | |
| Auditoría | — | — | Columnas comunes. |

Índices: `IX_NCL_MPB_VOLQUETE_01` sobre (`NPILA_ID`) y `IX_NCL_MPB_VOLQUETE_02` sobre (`DFECHA_PESAJE`, `CTURNO_CODIGO`).

**Parámetro:** `MPB_TONELAJE_MAX_VOLQUETE` en la tabla de parámetros del módulo.

**Procedimientos:** `planta.USP_MPB_INS_VOLQUETE`, `planta.USP_MPB_UPD_VOLQUETE`, `planta.USP_MPB_ANULA_VOLQUETE`. Los tres bloquean la fila de la pila (`UPDLOCK`) antes de validar su estado y actualizar acumulados.

**Vista:** `planta.VW_MPB_VOLQUETE_TURNO` (volquete + pila + labor + estado de la muestra).

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| POST | `/api/pilas/{id}/volquetes` | Registra volquete. | `PILA_NO_ABIERTA`, `TONELAJE_FUERA_DE_RANGO`, `FECHA_PESAJE_INVALIDA` |
| GET | `/api/volquetes?fecha=&turno=&pila=&pagina=` | Lista volquetes del turno. | — |
| PUT | `/api/volquetes/{id}` | Corrige volquete. | `PILA_NO_ABIERTA`, `MOTIVO_REQUERIDO`, `TONELAJE_FUERA_DE_RANGO` |
| POST | `/api/volquetes/{id}/anular` | Anula volquete. | `PILA_NO_ABIERTA`, `MOTIVO_REQUERIDO` |

Ejemplo `POST /api/pilas/340/volquetes`:

```json
// Solicitud
{ "placa": "ABC123", "tonelaje": 28.450, "fechaPesaje": "2026-10-15T09:42:00" }

// Respuesta 201
{ "id": 9812, "turno": "D", "pila": { "codigo": "PIL-TJ01-20261015-001",
  "tonelaje": 85.300, "volquetes": 3 } }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `RegistroVolquete.razor` | Formulario de captura rápida (placa, tonelaje, pila con búsqueda por código o labor); fecha/hora actual por defecto; muestra el acumulado de la pila al grabar. Pensado para tablet en balanza. |
| `VolquetesTurno.razor` | Grilla filtrable por fecha, turno y pila; columna de estado de muestra; edición con motivo obligatorio; acción Anular. |

## 9. Permisos

| Acción | Operador balanza | Supervisor | Geólogo/Plan. |
|--------|:---:|:---:|:---:|
| Registrar volquete | ✔ | ✔ | |
| Consultar volquetes | ✔ | ✔ | ✔ |
| Corregir / anular volquete | | ✔ | |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F02-01 | Registrar volquete de 28.45 t en pila `ABI`. | 201; acumulado y número de volquetes de la pila actualizados. | HU-F02-1 / CA1 |
| CP-F02-02 | Registrar volquete en pila `CER`. | 409 `PILA_NO_ABIERTA`. | HU-F02-1 / CA2 |
| CP-F02-03 | Registrar tonelaje 0, negativo y 45 t. | 422 `TONELAJE_FUERA_DE_RANGO`. | HU-F02-1 / CA3 |
| CP-F02-04 | Registrar con fecha de pesaje futura. | 422 `FECHA_PESAJE_INVALIDA`. | RN-F02-03 |
| CP-F02-05 | Filtrar volquetes por fecha, turno y pila. | Lista correcta con estado de muestra. | HU-F02-2 / CA1 |
| CP-F02-06 | Corregir tonelaje de 28.45 a 26.10 con motivo. | Acumulado recalculado; registro en auditoría con valor anterior y nuevo. | HU-F02-2 / CA2 |
| CP-F02-07 | Corregir sin motivo. | 422 `MOTIVO_REQUERIDO`. | RN-F02-06 |
| CP-F02-08 | Dos registros simultáneos en la misma pila. | Acumulado igual a la suma de ambos. | RN-F02-05 |

## 11. Preguntas abiertas

1. ¿Cuál es el tonelaje máximo real por tipo de volquete? ¿Hay más de un tipo?
2. ¿Los horarios de turno propuestos son los vigentes?
3. ¿Se quiere integrar la balanza en una fase posterior?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
