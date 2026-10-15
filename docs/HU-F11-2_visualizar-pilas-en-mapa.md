# HU-F11-2: Visualizar pilas en mapa

| Campo | Valor |
|-------|-------|
| Feature | F11 Integración SIG |
| Ranking WSJF de la feature | #11 (WSJF 1.2) |
| Depende de (feature) | F01 |
| Rol | Geólogo/Planeamiento |
| Story points | 5 |
| Incremento | Evolución |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Geología necesita ver espacialmente dónde están las pilas y su calidad para planificar.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Geólogo/Planeamiento, quiero ver las pilas sobre un mapa con su estado y ley, para tener una vista espacial del inventario.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado el mapa, cuando selecciono un polígono, entonces veo su código, labor, tonelaje, ley y estado.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Los polígonos se colorean por estado o por rango de ley.
- Al seleccionar un polígono se muestra su información resumida.

**Datos:** Geometría, código, labor, tonelaje, ley y estado de la pila.

**Interfaz y flujo:** Mapa interactivo con leyenda y panel de detalle.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Componente de mapa en Blazor (librería por definir).
- API: `GET /api/sig/pilas` en formato GeoJSON.
- Depende de HU-F11-1.
- Stack de referencia: Blazor + ASP.NET Core Web API + Dapper sobre SQL Server, según el estándar de desarrollo y de base de datos de CMH.
- Nombres preliminares: el esquema `planta` y el prefijo `MPB` (Muestreo de Pilas y Blending) están por confirmar con el DBA.

## Supuestos por validar

- Ninguno identificado.

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
