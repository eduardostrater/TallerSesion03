# SPEC-F05: Registro de blending a tolvas

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F05-1](../HU-F05-1_registrar-programa-de-blending-del-dia.md), [HU-F05-2](../HU-F05-2_registrar-carga-real-de-pila-a-tolva.md) |
| Prioridad WSJF | #7 (5.2); se construye antes que F06 por dependencia |
| Incremento | MVP 2: Blending |
| Depende de | F01, F04 |
| Habilita | F06, F08 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Planificar cuánto mineral de cada pila se envía a cada tolva y registrar los envíos reales, manteniendo el stock de las pilas al día.

## 2. Alcance

**Incluye:** catálogo de tolvas, programa de blending por fecha, registro de envíos reales y descuento de stock.
**No incluye:** optimización automática de la mezcla (posible evolución); consolidado del día (SPEC-F06).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Supervisor de planta | Elabora el programa de blending. |
| Operador de despacho | Registra los envíos reales. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F05-01 | Solo se programan y envían pilas en estado `CER` con stock mayor a 0. |
| RN-F05-02 | La suma programada de una pila en una fecha no puede superar su stock. |
| RN-F05-03 | Ley estimada de la tolva = Σ(ley de pila × t programadas) / Σ t programadas. |
| RN-F05-04 | Hay un programa por fecha de operación; se modifica mientras el día esté `ABI` (SPEC-F06). |
| RN-F05-05 | Cada envío descuenta su tonelaje del stock de la pila; no puede dejarlo negativo. |
| RN-F05-06 | Cuando el stock de la pila llega a 0, la pila pasa a `CON`. |
| RN-F05-07 | El envío copia la ley de la pila al momento del envío (la ley de una pila cerrada ya no cambia). |
| RN-F05-08 | No se registran envíos en días `CER`. Se pueden registrar envíos no programados, que se marcan como tales. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F05-01 | Mostrar las pilas disponibles (`CER` con stock) con su ley. | HU-F05-1 / CA1 |
| RF-F05-02 | Asignar toneladas por pila y tolva y ver la ley estimada por tolva al editar. | HU-F05-1 / CA1 |
| RF-F05-03 | Rechazar asignaciones que superen el stock. | HU-F05-1 / CA2 |
| RF-F05-04 | Registrar un envío real de pila a tolva. | HU-F05-2 / CA1 |
| RF-F05-05 | Marcar la pila como consumida al llegar a stock 0. | HU-F05-2 / CA2 |
| RF-F05-06 | Anular un envío con motivo, devolviendo el tonelaje al stock (solo con el día abierto). | RN-F05-08 |

## 6. Modelo de datos

**`planta.MPB_TOLVA`**: `NTOLVA_ID` INT PK, `CTOLVA_CODIGO` VARCHAR(10) único, `VTOLVA_NOMBRE` NVARCHAR(50), `NCAPACIDAD_CANTIDAD` DECIMAL(10,3), `BACTIVO` BIT.

**`planta.MPB_BLENDING_PROGRAMA`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NPROGRAMA_ID` | INT IDENTITY | No | PK. |
| `DFECHA_OPERACION` | DATE | No | |
| `NTOLVA_ID` | INT | No | FK a `MPB_TOLVA`. |
| `NPILA_ID` | INT | No | FK a `MPB_PILA`. |
| `NTONELAJE_CANTIDAD` | DECIMAL(12,3) | No | > 0. |
| Auditoría | — | — | |

Único `IX_UX_MPB_BLENDING_PROGRAMA_01` sobre (`DFECHA_OPERACION`, `NTOLVA_ID`, `NPILA_ID`).

**`planta.MPB_BLENDING_ENVIO`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NENVIO_ID` | INT IDENTITY | No | PK. |
| `DFECHA_OPERACION` | DATE | No | FK a `MPB_CARGA_DIA`. |
| `DFECHA_ENVIO` | DATETIME2 | No | |
| `NTOLVA_ID` | INT | No | FK. |
| `NPILA_ID` | INT | No | FK. |
| `NTONELAJE_CANTIDAD` | DECIMAL(12,3) | No | > 0. |
| `NLEY_AU` | DECIMAL(10,4) | No | Copia de la ley de la pila (RN-F05-07). |
| `BPROGRAMADO` | BIT | No | |
| `BANULADO` | BIT | No | Default 0. |
| Auditoría | — | — | |

**Procedimientos:** `planta.USP_MPB_GRABA_PROGRAMA` (reemplaza el programa de la fecha en una transacción), `planta.USP_MPB_INS_ENVIO` y `planta.USP_MPB_ANULA_ENVIO` (bloquean la pila con `UPDLOCK` antes de validar y descontar el stock).

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| GET | `/api/tolvas` | Catálogo de tolvas. | — |
| GET | `/api/blending/pilas-disponibles` | Pilas `CER` con stock y ley. | — |
| GET | `/api/blending/programa/{fecha}` | Programa de la fecha. | — |
| PUT | `/api/blending/programa/{fecha}` | Graba el programa completo. | `STOCK_INSUFICIENTE`, `PILA_NO_DISPONIBLE`, `DIA_CERRADO` |
| POST | `/api/blending/envios` | Registra envío. | `STOCK_INSUFICIENTE`, `PILA_NO_DISPONIBLE`, `DIA_CERRADO` |
| POST | `/api/blending/envios/{id}/anular` | Anula envío. | `DIA_CERRADO`, `MOTIVO_REQUERIDO` |

Ejemplo `POST /api/blending/envios`:

```json
// Solicitud
{ "fechaOperacion": "2026-10-20", "pilaId": 340, "tolvaId": 2, "tonelaje": 120.000 }

// Respuesta 201
{ "id": 551, "programado": true, "leyAu": 6.1000,
  "pila": { "codigo": "PIL-TJ01-20261015-001", "stock": 0.000, "estado": "CON" } }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `ProgramaBlending.razor` | Matriz pila × tolva para la fecha; columnas de stock y ley por pila; totales y ley estimada por tolva recalculados al editar; validación de stock en línea. |
| `EnvioBlending.razor` | Formulario de envío (pila, tolva, toneladas) con el programado y lo enviado a la fecha; muestra el stock restante. |

## 9. Permisos

| Acción | Supervisor | Op. despacho | Jefe de planta |
|--------|:---:|:---:|:---:|
| Elaborar programa | ✔ | | |
| Registrar envío | ✔ | ✔ | |
| Anular envío | ✔ | | |
| Consultar | ✔ | ✔ | ✔ |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F05-01 | Programar 2 pilas (100 t × 6 g/t, 50 t × 9 g/t) en la tolva 1. | Ley estimada de la tolva = 7.0000 g/t. | HU-F05-1 / CA1 |
| CP-F05-02 | Programar 150 t de una pila con 120 t de stock. | 422 `STOCK_INSUFICIENTE`. | HU-F05-1 / CA2 |
| CP-F05-03 | Programar una pila `ABI`. | 422 `PILA_NO_DISPONIBLE`. | RN-F05-01 |
| CP-F05-04 | Enviar 50 t de una pila con 120 t. | Stock = 70 t. | HU-F05-2 / CA1 |
| CP-F05-05 | Enviar las 70 t restantes. | Stock = 0; pila en `CON`. | HU-F05-2 / CA2 |
| CP-F05-06 | Dos envíos simultáneos de 70 t sobre una pila con 70 t. | Uno se acepta y el otro recibe `STOCK_INSUFICIENTE`. | RN-F05-05 |
| CP-F05-07 | Registrar envío en día cerrado. | 409 `DIA_CERRADO`. | RN-F05-08 |

## 11. Preguntas abiertas

1. ¿Cuántas tolvas hay y cuál es su capacidad?
2. ¿Existe una ley objetivo de cabeza que el programa deba respetar o alertar?
3. ¿Se permiten envíos no programados o deben bloquearse?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
