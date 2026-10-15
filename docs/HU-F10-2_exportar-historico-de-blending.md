# HU-F10-2: Exportar histórico de blending

| Campo | Valor |
|-------|-------|
| Feature | F10 Reportes |
| Ranking WSJF de la feature | #10 (WSJF 1.8) |
| Depende de (feature) | F04, F06 |
| Rol | Supervisor de planta |
| Story points | 2 |
| Incremento | Evolución |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Los reportes de gestión mensuales requieren el histórico de blending en Excel.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Supervisor de planta, quiero exportar a Excel el histórico de blending por rango de fechas, para preparar los reportes de gestión.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un rango de fechas, cuando exporto, entonces se descarga un archivo con el detalle por día, tolva y pila.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Se exporta por rango de fechas, con un máximo configurable (por ejemplo, 366 días).
- Detalle por día, tolva y pila, con toneladas y ley.

**Datos:** Fecha, tolva, pila, toneladas programadas, toneladas reales y ley.

**Interfaz y flujo:** Selector de rango de fechas y botón Exportar.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Generación del Excel en el servidor.
- API: `GET /api/blending/historico/excel?desde=&hasta=`.
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
