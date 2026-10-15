# HU-F01-1: Mantener catálogo de labores

| Campo | Valor |
|-------|-------|
| Feature | F01 Configuración de pilas |
| Ranking WSJF de la feature | #1 (WSJF 15.3) |
| Depende de (feature) | — |
| Rol | Geólogo/Planeamiento |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | En revisión |
| Versión | 0.3 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Hoy las labores se identifican de forma informal y con códigos distintos entre áreas, lo que impide atribuir el mineral y la ley a su origen. El negocio necesita un catálogo único de labores como base para todo el control de pilas.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Geólogo/Planeamiento, quiero registrar, editar y desactivar labores con su tipo y zona, para que las pilas y los volquetes se asocien siempre a una labor válida y se pueda reportar la producción por tipo de labor.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un código de labor nuevo, cuando lo registro con nombre, tipo de labor, zona y estado, entonces queda disponible para crear pilas.
2. Dado un código ya existente, cuando intento registrarlo, entonces el sistema lo rechaza por duplicado.
3. Dada una labor con pilas abiertas, cuando intento desactivarla, entonces el sistema lo impide e indica el motivo.
4. Dado un código de labor con formato inválido (más de 10 caracteres o con caracteres especiales), cuando intento guardarlo, entonces el sistema lo rechaza e indica el formato esperado.
5. Dado el tipo de labor, cuando registro o edito una labor, entonces solo puedo elegirlo de un catálogo (Tajeo, Galería, Chimenea, Rampa, Otro).

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- El código de labor es único y no se puede modificar una vez que la labor tiene pilas asociadas.
- Una labor solo puede desactivarse si no tiene pilas en estado Abierta.
- El tipo de labor se elige de un catálogo cerrado (Tajeo, Galería, Chimenea, Rampa, Otro).
- Una labor desactivada no aparece al crear pilas, pero se conserva para consultas históricas.

**Datos:** Código, nombre, tipo de labor, zona, estado (activo/inactivo), usuario y fecha de creación/modificación.

**Interfaz y flujo:** Ventana de mantenimiento con grilla filtrable por zona, tipo y estado, y formulario de alta/edición.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tablas: `planta.MPB_LABOR` y `planta.MPB_LABOR_TIPO` (catálogo). Índice único `IX_UX_MPB_LABOR_01` sobre `CLABOR_CODIGO`.
- Procedimientos: `planta.USP_MPB_INS_LABOR`, `planta.USP_MPB_UPD_LABOR`, `planta.USP_MPB_DESACTIVA_LABOR` (valida pilas abiertas en el servidor).
- API: `GET/POST/PUT /api/labores`, `PATCH /api/labores/{id}/desactivar`.
- Front: página Blazor `Labores.razor`; acceso controlado por ventana SIA.
- Stack de referencia: Blazor + ASP.NET Core Web API + Dapper sobre SQL Server, según el estándar de desarrollo y de base de datos de CMH.
- Nombres preliminares: el esquema `planta` y el prefijo `MPB` (Muestreo de Pilas y Blending) están por confirmar con el DBA.

## Supuestos por validar

- El código de labor es alfanumérico, en mayúsculas y de hasta 10 caracteres.
- Los tipos de labor listados son una propuesta inicial; Geología debe confirmar el catálogo definitivo.

## Aprobaciones

| Capa | Responsable | Aprueba | Estado | Fecha |
|------|-------------|---------|--------|-------|
| Necesidad de negocio | Negocio | Negocio | Pendiente | |
| Historia de usuario | Negocio + equipo | Negocio | Pendiente | |
| Criterios de aceptación | Negocio + calidad | Negocio + calidad | Pendiente | |
| Especificación funcional | Equipo | BP | Pendiente | |
| Especificación técnica preliminar | Desarrollo | Responsable técnico | Pendiente | |

## Historial de cambios

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
| 0.2 | 2026-10-15 | Se agrega el tipo de labor al enunciado y al criterio 1, la validación del formato del código (criterio 4) y el catálogo de tipos (criterio 5). Story points de 2 a 3. Estado: En revisión. |
| 0.3 | 2026-10-15 | Se reestructura la historia según las capas de SDD: necesidad de negocio, historia, criterios de aceptación, especificación funcional y especificación técnica preliminar. Se agrega la tabla de aprobaciones. |

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
