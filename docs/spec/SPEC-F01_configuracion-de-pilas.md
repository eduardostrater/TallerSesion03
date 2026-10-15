# SPEC-F01: Configuración de pilas

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F01-1](../HU-F01-1_mantener-catalogo-de-labores.md), [HU-F01-2](../HU-F01-2_crear-pila-poligono-por-labor.md) |
| Prioridad WSJF | #1 (15.3) |
| Incremento | MVP 1: Ley de pila |
| Depende de | — |
| Habilita | F02, F05, F11 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Contar con un catálogo único de labores y registrar cada pila (polígono) asociada a su labor, como base de todo el control de tonelaje, ley y blending.

## 2. Alcance

**Incluye:** mantenimiento de labores y de su catálogo de tipos; alta y consulta de pilas.
**No incluye:** geometría del polígono (SPEC-F11), cierre de pila (SPEC-F04), registro de volquetes (SPEC-F02).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Geólogo/Planeamiento | Mantiene labores y crea pilas. |
| Supervisor de planta | Consulta labores y pilas. |
| Administrador | Mantiene el catálogo de tipos de labor. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F01-01 | El código de labor es único, alfanumérico, en mayúsculas y de 1 a 10 caracteres (`^[A-Z0-9]{1,10}$`). |
| RN-F01-02 | El código de labor no se puede modificar si la labor tiene pilas asociadas. |
| RN-F01-03 | El tipo de labor se elige del catálogo `MPB_LABOR_TIPO` (valores iniciales: Tajeo, Galería, Chimenea, Rampa, Otro). |
| RN-F01-04 | Una labor solo se desactiva si no tiene pilas en estado `ABI`. |
| RN-F01-05 | Una labor inactiva no aparece al crear pilas, pero se mantiene en consultas históricas. |
| RN-F01-06 | Una pila pertenece a una sola labor activa. |
| RN-F01-07 | El código de pila lo genera el sistema: `PIL-<código labor>-<AAAAMMDD>-<correlativo de 3 dígitos por labor y día>`. |
| RN-F01-08 | La pila nace en estado `ABI`, con tonelaje, stock y número de volquetes en 0. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F01-01 | Registrar una labor con código, nombre, tipo, zona y estado. | HU-F01-1 / CA1, CA5 |
| RF-F01-02 | Rechazar códigos duplicados o con formato inválido, indicando el formato esperado. | HU-F01-1 / CA2, CA4 |
| RF-F01-03 | Editar nombre, tipo y zona de una labor; el código solo si no tiene pilas. | HU-F01-1 |
| RF-F01-04 | Desactivar una labor validando RN-F01-04 e indicando el motivo si se rechaza. | HU-F01-1 / CA3 |
| RF-F01-05 | Listar labores con filtro por zona, tipo y estado, paginado en el servidor. | HU-F01-1 |
| RF-F01-06 | Crear una pila para una labor activa con su código de polígono. | HU-F01-2 / CA1 |
| RF-F01-07 | Consultar una pila con labor, fecha de creación, estado, tonelaje y número de volquetes. | HU-F01-2 / CA2 |
| RF-F01-08 | Listar pilas con filtro por labor, estado y rango de fechas. | HU-F01-2 |

## 6. Modelo de datos

**`planta.MPB_LABOR_TIPO`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NLABOR_TIPO_ID` | INT | No | PK. |
| `CLABOR_TIPO_CODIGO` | VARCHAR(10) | No | Código único (TAJ, GAL, CHI, RAM, OTR). |
| `VLABOR_TIPO_NOMBRE` | NVARCHAR(50) | No | Nombre visible. |
| `BACTIVO` | BIT | No | Default 1. |

**`planta.MPB_LABOR`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NLABOR_ID` | INT IDENTITY | No | PK `PK_MPB_LABOR`. |
| `CLABOR_CODIGO` | VARCHAR(10) | No | Único (`IX_UX_MPB_LABOR_01`). |
| `VLABOR_NOMBRE` | NVARCHAR(100) | No | |
| `NLABOR_TIPO_ID` | INT | No | FK `FK_MPB_LABOR_MPB_LABOR_TIPO`. |
| `CZONA_CODIGO` | VARCHAR(10) | No | Zona de la mina. |
| `BACTIVO` | BIT | No | Default 1. |
| Auditoría | — | — | Columnas comunes. |

**`planta.MPB_PILA`** (columnas de esta spec; F02, F04 y F05 usan los acumulados)

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NPILA_ID` | INT IDENTITY | No | PK `PK_MPB_PILA`. |
| `CPILA_CODIGO` | VARCHAR(30) | No | Único (`IX_UX_MPB_PILA_01`). |
| `NLABOR_ID` | INT | No | FK `FK_MPB_PILA_MPB_LABOR`. |
| `CPOLIGONO_CODIGO` | VARCHAR(30) | No | Código del polígono en el SIG. |
| `CPILA_ESTADO` | CHAR(3) | No | `ABI`, `CER`, `CON`. Default `ABI`. |
| `NTONELAJE_CANTIDAD` | DECIMAL(12,3) | No | Acumulado de volquetes. Default 0. |
| `NVOLQUETE_CANTIDAD` | INT | No | Default 0. |
| `NSTOCK_CANTIDAD` | DECIMAL(12,3) | No | Saldo disponible para blending. Default 0. |
| `NLEY_AU` | DECIMAL(10,4) | Sí | Ley ponderada (SPEC-F04). |
| `NTONELAJE_CON_LEY` | DECIMAL(12,3) | No | Default 0 (SPEC-F04). |
| `DFECHA_CIERRE` | DATETIME2 | Sí | SPEC-F04. |
| `CUSUARIO_CIERRE` | VARCHAR(50) | Sí | SPEC-F04. |
| Auditoría | — | — | Columnas comunes. |

Índice: `IX_NCL_MPB_PILA_02` sobre (`NLABOR_ID`, `CPILA_ESTADO`).

**Procedimientos:** `planta.USP_MPB_INS_LABOR`, `planta.USP_MPB_UPD_LABOR`, `planta.USP_MPB_DESACTIVA_LABOR`, `planta.USP_MPB_INS_PILA` (genera el correlativo dentro de la transacción con bloqueo `UPDLOCK, HOLDLOCK` para evitar códigos duplicados en altas simultáneas).

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| GET | `/api/labores?zona=&tipo=&activo=&pagina=` | Lista labores. | — |
| POST | `/api/labores` | Crea labor. | `LABOR_DUPLICADA`, `LABOR_CODIGO_INVALIDO`, `TIPO_INEXISTENTE` |
| PUT | `/api/labores/{id}` | Edita labor. | `LABOR_CON_PILAS` (si cambia el código) |
| PATCH | `/api/labores/{id}/desactivar` | Desactiva labor. | `LABOR_CON_PILAS_ABIERTAS` |
| GET | `/api/labores/tipos` | Catálogo de tipos. | — |
| GET | `/api/pilas?labor=&estado=&desde=&hasta=&pagina=` | Lista pilas. | — |
| GET | `/api/pilas/{id}` | Detalle de pila. | `PILA_INEXISTENTE` |
| POST | `/api/pilas` | Crea pila. | `LABOR_INACTIVA` |

Ejemplo `POST /api/pilas`:

```json
// Solicitud
{ "laborId": 12, "poligonoCodigo": "POL-2026-118" }

// Respuesta 201
{ "id": 340, "codigo": "PIL-TJ01-20261015-001", "estado": "ABI",
  "laborCodigo": "TJ01", "tonelaje": 0, "volquetes": 0 }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `Labores.razor` | Grilla con filtros (zona, tipo, estado); botones Nueva, Editar, Desactivar. Formulario modal con validación del código en línea. |
| `Pilas.razor` | Grilla con filtros (labor, estado, fechas); botón Nueva pila (labor + código de polígono); acceso al detalle. |

## 9. Permisos

| Acción | Geólogo/Plan. | Supervisor | Administrador |
|--------|:---:|:---:|:---:|
| Consultar labores y pilas | ✔ | ✔ | ✔ |
| Crear / editar / desactivar labor | ✔ | | ✔ |
| Mantener tipos de labor | | | ✔ |
| Crear pila | ✔ | | |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F01-01 | Crear labor `TJ01` con todos los datos. | 201; labor disponible al crear pilas. | HU-F01-1 / CA1 |
| CP-F01-02 | Crear otra labor `TJ01`. | 409 `LABOR_DUPLICADA`. | HU-F01-1 / CA2 |
| CP-F01-03 | Desactivar labor con una pila `ABI`. | 409 `LABOR_CON_PILAS_ABIERTAS`. | HU-F01-1 / CA3 |
| CP-F01-04 | Crear labor `TJ-01#` y otra de 11 caracteres. | 422 `LABOR_CODIGO_INVALIDO`. | HU-F01-1 / CA4 |
| CP-F01-05 | Enviar un tipo de labor inexistente. | 422 `TIPO_INEXISTENTE`. | HU-F01-1 / CA5 |
| CP-F01-06 | Crear pila para labor activa. | 201; código generado, estado `ABI`, 0 volquetes. | HU-F01-2 / CA1, CA2 |
| CP-F01-07 | Crear pila para labor inactiva. | 409 `LABOR_INACTIVA`. | HU-F01-2 |
| CP-F01-08 | Crear dos pilas en paralelo para la misma labor y día. | Códigos distintos y correlativos. | RN-F01-07 |

## 11. Preguntas abiertas

1. ¿El formato del código de labor (10 caracteres alfanuméricos) coincide con el que ya usa Geología?
2. ¿El catálogo de zonas existe en otro sistema o se crea en este módulo?
3. ¿El formato propuesto para el código de pila es aceptable?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
