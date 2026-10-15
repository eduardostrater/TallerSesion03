# HU-F09-2: Auditar cambios en registros críticos

| Campo | Valor |
|-------|-------|
| Feature | F09 Seguridad y roles |
| Ranking WSJF de la feature | #5 (WSJF 7.0) |
| Depende de (feature) | — |
| Rol | Jefe de planta |
| Story points | 2 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Ante una auditoría o una diferencia de producción, hoy no se puede saber quién cambió un tonelaje o una ley. El negocio necesita trazabilidad de los cambios en los datos críticos.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Jefe de planta, quiero consultar quién modificó volquetes, leyes y pilas, para asegurar la trazabilidad ante auditorías.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un cambio en un volquete, una ley o una pila, cuando se graba, entonces se registran el usuario, la fecha/hora, el valor anterior y el valor nuevo.
2. Dado un registro, cuando consulto su historial, entonces veo todos sus cambios en orden cronológico.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Se auditan las altas, modificaciones y cierres de pilas, volquetes, muestras y envíos de blending.
- Cada registro guarda usuario, fecha y hora, valor anterior y valor nuevo.
- La auditoría no se puede modificar ni eliminar desde la aplicación.

**Datos:** Entidad, ID del registro, campo, valor anterior, valor nuevo, usuario, fecha y hora.

**Interfaz y flujo:** Pestaña Historial en el detalle de cada registro, en orden cronológico.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `planta.MPB_REGISTRO_AUD`.
- La auditoría se escribe dentro de los procedimientos de modificación (se evitan triggers).
- API: `GET /api/auditoria?entidad=&id=`.
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
