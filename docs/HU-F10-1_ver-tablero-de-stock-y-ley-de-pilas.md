# HU-F10-1: Ver tablero de stock y ley de pilas

| Campo | Valor |
|-------|-------|
| Feature | F10 Reportes |
| Ranking WSJF de la feature | #10 (WSJF 1.8) |
| Depende de (feature) | F04, F06 |
| Rol | Jefe de planta |
| Story points | 5 |
| Incremento | Evolución |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

La jefatura no tiene una vista consolidada del inventario de pilas para decidir el blending de los próximos días.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Jefe de planta, quiero un tablero con el stock y la ley de las pilas por estado, para decidir el blending de los próximos días.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado el tablero, cuando lo abro, entonces veo las pilas abiertas, cerradas y consumidas con su tonelaje y ley.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Se agrupan las pilas por estado (Abierta, Cerrada, Consumida).
- Se muestran el tonelaje y la ley ponderada por estado y por labor.

**Datos:** Pilas con estado, labor, tonelaje, stock y ley.

**Interfaz y flujo:** Tablero con indicadores (stock total, ley promedio) y tabla filtrable.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Vista: `planta.VW_MPB_TABLERO_PILAS`.
- API: `GET /api/tablero/pilas`.
- Front: página Blazor con el sistema de diseño corporativo.
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
