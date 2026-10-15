# HU-F06-2: Comparar programado vs. real

| Campo | Valor |
|-------|-------|
| Feature | F06 Carga del día |
| Ranking WSJF de la feature | #6 (WSJF 5.3) |
| Depende de (feature) | F05 |
| Rol | Jefe de planta |
| Story points | 2 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

La jefatura no puede medir con facilidad cuánto se cumple el programa de blending. El negocio necesita comparar lo programado con lo real para corregir desviaciones.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Jefe de planta, quiero comparar el blending programado con el real, para medir el cumplimiento del programa.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una fecha, cuando consulto, entonces veo por pila y por tolva las toneladas programadas, las reales y la desviación %.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Desviación % = (real − programado) / programado × 100, por pila y por tolva.
- Se resaltan las desviaciones que superen un umbral configurable.

**Datos:** Fecha, pila, tolva, toneladas programadas, toneladas reales y desviación %.

**Interfaz y flujo:** Tabla comparativa con resaltado por umbral y totales por tolva.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Vista: `planta.VW_MPB_BLENDING_CUMPLIMIENTO`.
- API: `GET /api/blending/cumplimiento/{fecha}`.
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
