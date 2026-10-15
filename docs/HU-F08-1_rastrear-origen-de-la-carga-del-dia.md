# HU-F08-1: Rastrear origen de la carga del día

| Campo | Valor |
|-------|-------|
| Feature | F08 Trazabilidad |
| Ranking WSJF de la feature | #8 (WSJF 3.8) |
| Depende de (feature) | F05 |
| Rol | Jefe de planta |
| Story points | 3 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Cuando la ley de cabeza sale fuera de lo esperado, no se puede identificar rápidamente de qué labores provino el mineral. El negocio necesita rastrear la carga del día hasta su origen.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Jefe de planta, quiero navegar desde la carga del día hasta las tolvas, las pilas, los volquetes y las labores, para conocer de dónde vino el mineral procesado.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una carga diaria, cuando la abro, entonces puedo descender hasta cada volquete y su muestra.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- La navegación sigue la cadena carga del día → tolva → envío → pila → volquete → muestra → labor.
- La trazabilidad es de solo consulta.

**Datos:** Todas las entidades de la cadena, con sus tonelajes y leyes.

**Interfaz y flujo:** Vista jerárquica expandible desde la carga del día.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Vista: `planta.VW_MPB_TRAZABILIDAD_ORIGEN`; consulta paginada por nivel para no cargar todo el árbol.
- API: `GET /api/trazabilidad/carga-dia/{fecha}`.
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
