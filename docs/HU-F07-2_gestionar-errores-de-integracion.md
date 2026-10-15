# HU-F07-2: Gestionar errores de integración

| Campo | Valor |
|-------|-------|
| Feature | F07 Integración laboratorio |
| Ranking WSJF de la feature | #9 (WSJF 2.4) |
| Depende de (feature) | F03 |
| Rol | Administrador |
| Story points | 3 |
| Incremento | Evolución |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Si la integración falla en silencio, las pilas quedan sin ley y se retrasa el cierre. El negocio necesita ver y resolver los resultados rechazados.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Administrador, quiero ver los resultados rechazados por la integración, para corregirlos y reprocesarlos.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un resultado con un código de muestra inexistente, entonces queda en la bandeja de errores con el motivo.
2. Dado un error corregido, cuando lo reproceso, entonces se aplica a la muestra.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Cada rechazo guarda el motivo (muestra inexistente, valor inválido, duplicado).
- Un error corregido se puede reprocesar; al aplicarse sale de la bandeja.

**Datos:** Resultado recibido, motivo, fecha, estado (pendiente/reprocesado) y usuario que reprocesa.

**Interfaz y flujo:** Bandeja de errores con filtro por motivo y acción Reprocesar.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `etl.MPB_LAB_RESULTADO_LOG`.
- API: `GET /api/integracion/laboratorio/errores`, `POST .../{id}/reprocesar`.
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
