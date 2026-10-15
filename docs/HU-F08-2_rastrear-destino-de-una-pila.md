# HU-F08-2: Rastrear destino de una pila

| Campo | Valor |
|-------|-------|
| Feature | F08 Trazabilidad |
| Ranking WSJF de la feature | #8 (WSJF 3.8) |
| Depende de (feature) | F05 |
| Rol | Geólogo/Planeamiento |
| Story points | 2 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Para conciliar la producción por labor, Planeamiento necesita saber a dónde fue el mineral de cada pila.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Geólogo/Planeamiento, quiero ver a qué tolvas y días se envió una pila, para conciliar la producción por labor.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una pila, cuando consulto su destino, entonces veo los envíos por fecha y tolva y el saldo pendiente.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Se muestran todos los envíos de la pila por fecha y tolva.
- Saldo = tonelaje final de la pila − Σ toneladas enviadas.

**Datos:** Pila, fecha, tolva, toneladas enviadas y saldo.

**Interfaz y flujo:** Pestaña Destino en el detalle de la pila.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Vista: `planta.VW_MPB_PILA_DESTINO`.
- API: `GET /api/pilas/{id}/destino`.
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
