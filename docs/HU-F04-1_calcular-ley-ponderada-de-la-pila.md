# HU-F04-1: Calcular ley ponderada de la pila

| Campo | Valor |
|-------|-------|
| Feature | F04 Ley ponderada |
| Ranking WSJF de la feature | #4 (WSJF 7.2) |
| Depende de (feature) | F03 |
| Rol | Supervisor de planta |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

La ley de cada pila se calcula hoy en hojas de cálculo, con riesgo de error y desfase. El negocio necesita conocer la ley ponderada de cada pila en todo momento para planificar el blending.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Supervisor de planta, quiero ver la ley ponderada de cada pila, para conocer la calidad del mineral disponible.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una pila con muestras "Con ley", cuando la consulto, entonces la ley = Σ(ley × tonelaje) / Σ tonelaje de esos volquetes.
2. Dada una pila con muestras pendientes, cuando la consulto, entonces se muestra la ley parcial y el % de tonelaje con ley.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Ley de pila = Σ(ley × tonelaje) / Σ tonelaje, solo con las muestras en estado Con ley.
- Si hay muestras pendientes, se muestra la ley parcial y el % de tonelaje con ley.
- El cálculo se hace siempre en el servidor; el cliente solo lo muestra.

**Datos:** Pila, tonelaje total, tonelaje con ley, % de cobertura y ley ponderada.

**Interfaz y flujo:** Columna de ley y % de cobertura en la ventana de pilas; detalle por volquete al abrir la pila.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Función: `planta.FN_MPB_LEY_PONDERADA_PILA` o vista `planta.VW_MPB_PILA_LEY`.
- Se persiste la ley calculada en `MPB_PILA` al registrar cada ley para no recalcular en cada consulta.
- API: `GET /api/pilas/{id}/ley`.
- Prueba unitaria del cálculo con casos de 0, 1 y N muestras.
- Stack de referencia: Blazor + ASP.NET Core Web API + Dapper sobre SQL Server, según el estándar de desarrollo y de base de datos de CMH.
- Nombres preliminares: el esquema `planta` y el prefijo `MPB` (Muestreo de Pilas y Blending) están por confirmar con el DBA.

## Supuestos por validar

- Se pondera por tonelaje húmedo (por confirmar con el negocio).

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
