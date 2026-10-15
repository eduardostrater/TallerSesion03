# HU-F02-2: Consultar y corregir volquetes del turno

| Campo | Valor |
|-------|-------|
| Feature | F02 Registro de volquetes |
| Ranking WSJF de la feature | #2 (WSJF 13.0) |
| Depende de (feature) | F01 |
| Rol | Supervisor de planta |
| Story points | 2 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Los errores de digitación en balanza distorsionan el tonelaje y la ley de la pila. El supervisor necesita revisar y corregir los registros del turno antes de que la pila se cierre.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Supervisor de planta, quiero listar los volquetes del turno y corregir datos mal registrados, para asegurar que el tonelaje de la pila sea confiable.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un turno, cuando filtro por fecha y pila, entonces veo los volquetes con su tonelaje y el estado de su muestra.
2. Dado un volquete de una pila abierta, cuando corrijo el tonelaje, entonces se recalcula el acumulado y se registra la auditoría.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Solo se pueden corregir volquetes de pilas en estado Abierta.
- Toda corrección recalcula el acumulado de la pila y queda auditada (valor anterior y nuevo).
- La corrección exige un motivo.

**Datos:** Volquete, tonelaje anterior y nuevo, motivo, usuario y fecha de la corrección.

**Interfaz y flujo:** Grilla de volquetes del turno con filtros (fecha, turno, pila) y estado de la muestra; edición en línea con motivo obligatorio.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Procedimiento: `planta.USP_MPB_UPD_VOLQUETE` (actualiza, recalcula y registra auditoría en una sola transacción).
- Vista: `planta.VW_MPB_VOLQUETE_TURNO`.
- API: `GET /api/volquetes?fecha=&turno=&pila=`, `PUT /api/volquetes/{id}`.
- Front: página Blazor `VolquetesTurno.razor`.
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
