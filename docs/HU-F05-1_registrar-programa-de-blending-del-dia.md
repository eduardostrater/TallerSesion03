# HU-F05-1: Registrar programa de blending del día

| Campo | Valor |
|-------|-------|
| Feature | F05 Blending a tolvas |
| Ranking WSJF de la feature | #7 (WSJF 5.2) |
| Depende de (feature) | F01, F04 |
| Rol | Supervisor de planta |
| Story points | 3 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

El programa de blending se define en reuniones y hojas de cálculo, sin validar el stock ni la ley resultante. El negocio necesita planificar la carga de cada tolva con las pilas disponibles y su ley.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Supervisor de planta, quiero definir cuántas toneladas se enviarán de cada pila a cada tolva, para planificar la carga del día con la ley objetivo.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dadas pilas "Cerradas" con stock, cuando asigno toneladas a una tolva, entonces el sistema muestra la ley estimada de la mezcla.
2. Dada una asignación mayor al stock de la pila, cuando guardo, entonces el sistema lo rechaza.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Solo se programan pilas en estado Cerrada con stock disponible.
- Lo programado de una pila no puede superar su stock.
- La ley estimada de la tolva = Σ(ley de pila × toneladas) / Σ toneladas.
- Hay un programa por fecha; se puede modificar mientras el día no esté cerrado.

**Datos:** Fecha, tolva, pila, toneladas programadas y ley estimada.

**Interfaz y flujo:** Matriz pila × tolva con stock disponible y ley estimada recalculada al editar.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tablas: `planta.MPB_TOLVA` (catálogo) y `planta.MPB_BLENDING_PROGRAMA`.
- Procedimiento: `planta.USP_MPB_GRABA_PROGRAMA` (valida stock en el servidor).
- API: `GET/PUT /api/blending/programa/{fecha}`.
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
