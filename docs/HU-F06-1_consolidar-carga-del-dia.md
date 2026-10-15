# HU-F06-1: Consolidar carga del día

| Campo | Valor |
|-------|-------|
| Feature | F06 Carga del día |
| Ranking WSJF de la feature | #6 (WSJF 5.3) |
| Depende de (feature) | F05 |
| Rol | Supervisor de planta |
| Story points | 3 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

La carga diaria a planta y su ley de cabeza se consolidan manualmente al final del día. El negocio necesita obtener ese consolidado de forma automática y confiable.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Supervisor de planta, quiero ver la suma de todas las tolvas del día con su ley de cabeza ponderada, para reportar la alimentación a planta.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una fecha, cuando consulto, entonces veo el tonelaje por tolva, el tonelaje total y la ley ponderada del día.
2. Dado un día cerrado, no se pueden registrar más envíos a tolvas en esa fecha.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Carga del día = suma de los envíos reales a todas las tolvas en la fecha.
- Ley de cabeza = promedio ponderado por tonelaje de las leyes de las pilas enviadas.
- Al cerrar el día no se admiten más envíos en esa fecha.

**Datos:** Fecha, tonelaje por tolva, tonelaje total, ley de cabeza, estado del día y usuario que cierra.

**Interfaz y flujo:** Resumen diario por tolva con totales y botón Cerrar día.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `planta.MPB_CARGA_DIA`; vista `planta.VW_MPB_CARGA_DIA_TOLVA`.
- Procedimiento: `planta.USP_MPB_CIERRA_DIA`.
- API: `GET /api/carga-dia/{fecha}`, `POST /api/carga-dia/{fecha}/cerrar`.
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
