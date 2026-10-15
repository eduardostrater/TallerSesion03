# HU-F11-1: Importar polígonos desde el SIG

| Campo | Valor |
|-------|-------|
| Feature | F11 Integración SIG |
| Ranking WSJF de la feature | #11 (WSJF 1.2) |
| Depende de (feature) | F01 |
| Rol | Geólogo/Planeamiento |
| Story points | 8 |
| Incremento | Evolución |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Las pilas se definen en el SIG y luego se vuelven a registrar a mano en el sistema. El negocio necesita importar los polígonos para evitar esa doble digitación.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Geólogo/Planeamiento, quiero importar los polígonos definidos en el SIG, para no registrar las pilas manualmente.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un archivo o servicio del SIG [supuesto: formato por definir], cuando importo, entonces se crean o actualizan las pilas con su geometría.
2. Dado un polígono con una labor inexistente, entonces se reporta como error.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Cada polígono se asocia a una labor existente; si no existe, se reporta como error.
- Si el polígono ya existe, se actualiza su geometría; si no, se crea la pila.
- La importación muestra un resumen de creados, actualizados y rechazados.

**Datos:** Código de polígono, labor, geometría y fecha de importación.

**Interfaz y flujo:** Pantalla de importación con vista previa y resumen de resultados.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Formato de intercambio por definir con Geología (GeoJSON o shapefile, o un servicio del SIG).
- Columna de geometría en `planta.MPB_PILA` (tipo `geometry` de SQL Server).
- API: `POST /api/sig/importar`.
- Stack de referencia: Blazor + ASP.NET Core Web API + Dapper sobre SQL Server, según el estándar de desarrollo y de base de datos de CMH.
- Nombres preliminares: el esquema `planta` y el prefijo `MPB` (Muestreo de Pilas y Blending) están por confirmar con el DBA.

## Supuestos por validar

- El formato de intercambio con el SIG (archivo o servicio) está por definir con Geología.

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
| 0.2 | 2026-10-15 | Se reestructura la historia según las capas de SDD: necesidad de negocio, historia, criterios de aceptación, especificación funcional y especificación técnica preliminar. Se agrega la tabla de aprobaciones. |

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
