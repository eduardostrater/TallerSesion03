# HU-F04-2: Cerrar pila con ley final

| Campo | Valor |
|-------|-------|
| Feature | F04 Ley ponderada |
| Ranking WSJF de la feature | #4 (WSJF 7.2) |
| Depende de (feature) | F03 |
| Rol | Geólogo/Planeamiento |
| Story points | 2 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Sin un cierre formal, las pilas siguen recibiendo carga después de calcular su ley y el dato pierde validez. El negocio necesita congelar la ley y el tonelaje de una pila antes de usarla en el blending.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Geólogo/Planeamiento, quiero cerrar una pila cuando todas sus muestras tengan ley, para que quede disponible para el blending con una ley definitiva.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una pila con 100 % de muestras con ley, cuando la cierro, entonces pasa a "Cerrada" con su ley y tonelaje finales.
2. Dada una pila con muestras pendientes, cuando intento cerrarla, entonces el sistema lo impide e indica cuántas faltan.
3. Dada una pila cerrada, no se pueden agregar ni modificar volquetes.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Solo se cierra una pila con el 100 % de sus muestras en estado Con ley.
- Al cerrar se fijan la ley final, el tonelaje final y la fecha de cierre.
- Una pila cerrada no admite nuevos volquetes ni correcciones (candado de negocio).
- Solo el rol Geólogo/Planeamiento puede cerrar pilas.

**Datos:** Pila, ley final, tonelaje final, fecha de cierre y usuario que cierra.

**Interfaz y flujo:** Botón Cerrar pila, habilitado solo cuando la cobertura es 100 %; si no, muestra cuántas muestras faltan.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Procedimiento: `planta.USP_MPB_CIERRA_PILA` (revalida la cobertura dentro de la transacción).
- Los procedimientos de volquete y ley rechazan cambios sobre pilas cerradas.
- API: `POST /api/pilas/{id}/cerrar`.
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
