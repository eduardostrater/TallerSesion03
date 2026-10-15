# SPEC-F09: Seguridad y auditoría

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F09-1](../HU-F09-1_controlar-acceso-por-ventana.md), [HU-F09-2](../HU-F09-2_auditar-cambios-en-registros-criticos.md) |
| Prioridad WSJF | #5 (7.0) |
| Incremento | MVP 1: Ley de pila |
| Depende de | — (transversal a todas las specs) |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Garantizar que cada usuario solo realice las acciones de su rol y que todo cambio en datos críticos quede registrado de forma inalterable.

## 2. Alcance

**Incluye:** registro de ventanas y acciones en SIA, autorización en la API, auditoría de cambios y su consulta.
**No incluye:** pantalla propia de gestión de usuarios y permisos (se usa SIA).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Administrador | Asigna permisos en SIA. |
| Jefe de planta | Consulta la auditoría. |
| Todos los roles | Sujetos a control de acceso. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F09-01 | El acceso se define por ventana y por acción: `CON` (consultar), `REG` (registrar), `MOD` (modificar/anular), `CER` (cerrar). |
| RN-F09-02 | La autorización se valida en cada endpoint de la API; ocultar botones en la interfaz es solo una ayuda visual. |
| RN-F09-03 | Se auditan: labor, pila, volquete, muestra, programa de blending, envío y cierre de día. |
| RN-F09-04 | Cada registro de auditoría guarda entidad, ID, campo, valor anterior, valor nuevo, motivo (si aplica), usuario, fecha y hora. |
| RN-F09-05 | La auditoría es de solo inserción: la aplicación no la modifica ni la elimina. |
| RN-F09-06 | La auditoría se escribe en la misma transacción que el cambio; si falla la auditoría, falla el cambio. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F09-01 | Denegar el ingreso a una ventana sin permiso `CON`. | HU-F09-1 / CA1 |
| RF-F09-02 | Rechazar en el servidor una operación sin el permiso de la acción. | HU-F09-1 / CA2 |
| RF-F09-03 | Registrar la auditoría de cada alta, modificación, anulación y cierre. | HU-F09-2 / CA1 |
| RF-F09-04 | Consultar el historial de un registro en orden cronológico. | HU-F09-2 / CA2 |

## 6. Matriz de permisos inicial

| Ventana | Geólogo/Plan. | Muestrero | Op. balanza/despacho | Analista lab. | Supervisor | Jefe de planta | Admin. |
|---------|---|---|---|---|---|---|---|
| `MPB_LABORES` | CON REG MOD | | | | CON | CON | CON REG MOD |
| `MPB_PILAS` | CON REG CER | CON | CON | CON | CON | CON | CON |
| `MPB_VOLQUETES` | CON | CON | CON REG | | CON REG MOD | CON | CON |
| `MPB_MUESTRAS` | CON | CON REG | | CON REG MOD | CON REG | CON | CON |
| `MPB_BLENDING` | CON | | CON REG | | CON REG MOD | CON | CON |
| `MPB_CARGA_DIA` | CON | | CON | | CON CER | CON | CON |
| `MPB_TRAZABILIDAD` | CON | | | | CON | CON | CON |
| `MPB_AUDITORIA` | | | | | | CON | CON |
| `MPB_INTEGRACION` | | | | CON | | | CON MOD |
| `MPB_TABLERO` | CON | | | | CON | CON | CON |
| `MPB_SIG` | CON REG | | | | CON | CON | CON |

## 7. Modelo de datos

**`planta.MPB_REGISTRO_AUD`**

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `NREGISTRO_AUD_ID` | BIGINT IDENTITY | No | PK. |
| `CENTIDAD_CODIGO` | VARCHAR(30) | No | `LABOR`, `PILA`, `VOLQUETE`, `MUESTRA`, `PROGRAMA`, `ENVIO`, `CARGA_DIA`. |
| `NREGISTRO_ID` | BIGINT | No | ID del registro auditado. |
| `CACCION_CODIGO` | CHAR(3) | No | `INS`, `UPD`, `ANU`, `CER`. |
| `CCAMPO_NOMBRE` | VARCHAR(50) | Sí | Nulo en altas y cierres. |
| `VVALOR_ANTERIOR` | NVARCHAR(200) | Sí | |
| `VVALOR_NUEVO` | NVARCHAR(200) | Sí | |
| `VMOTIVO_DESCRIPCION` | NVARCHAR(200) | Sí | |
| `CUSUARIO_CREACION` | VARCHAR(50) | No | |
| `DFECHA_CREACION` | DATETIME2 | No | Default `SYSDATETIME()`. |

Índice: `IX_NCL_MPB_REGISTRO_AUD_01` sobre (`CENTIDAD_CODIGO`, `NREGISTRO_ID`, `DFECHA_CREACION`).

**Procedimiento:** `planta.USP_MPB_INS_AUDITORIA`, invocado por todos los procedimientos de modificación (no se usan triggers).

**Seguridad en base de datos:** el usuario de la aplicación solo tiene `INSERT` y `SELECT` sobre `MPB_REGISTRO_AUD`, sin `UPDATE` ni `DELETE`.

## 8. Contrato de API

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/api/auditoria?entidad=&id=&pagina=` | Historial de un registro. |
| GET | `/api/auditoria/buscar?usuario=&desde=&hasta=&entidad=` | Búsqueda general (solo Jefe de planta y Administrador). |

Errores comunes: 401 (sin sesión) y 403 (sin permiso para la acción).

## 9. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| Pestaña Historial | En el detalle de labor, pila, volquete y muestra: lista cronológica de cambios. |
| `Auditoria.razor` | Búsqueda general por usuario, entidad y rango de fechas. |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F09-01 | Muestrero intenta abrir `MPB_LABORES`. | Acceso denegado. | HU-F09-1 / CA1 |
| CP-F09-02 | Operador llama a `PUT /api/volquetes/{id}` directamente. | 403. | HU-F09-1 / CA2 |
| CP-F09-03 | Supervisor corrige un tonelaje. | Registro en auditoría con valor anterior, nuevo, motivo y usuario. | HU-F09-2 / CA1 |
| CP-F09-04 | Consultar historial de un volquete con 3 cambios. | 3 registros en orden cronológico. | HU-F09-2 / CA2 |
| CP-F09-05 | Forzar un error al insertar la auditoría. | El cambio original no se graba. | RN-F09-06 |
| CP-F09-06 | Intentar `UPDATE` sobre la auditoría con el usuario de la aplicación. | Permiso denegado en la base de datos. | RN-F09-05 |

## 11. Preguntas abiertas

1. ¿La matriz de permisos propuesta refleja la organización real?
2. ¿Cuánto tiempo se debe conservar la auditoría?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
