# HU-F07-1: Recibir leyes automáticamente

| Campo | Valor |
|-------|-------|
| Feature | F07 Integración laboratorio |
| Ranking WSJF de la feature | #9 (WSJF 2.4) |
| Depende de (feature) | F03 |
| Rol | Analista de laboratorio |
| Story points | 8 |
| Incremento | Evolución |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

La digitación manual de leyes genera retrasos y errores de transcripción. El negocio necesita que los resultados del laboratorio lleguen automáticamente al sistema.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Analista de laboratorio, quiero que las leyes se carguen automáticamente desde el sistema de laboratorio, para evitar la doble digitación.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un resultado emitido por el laboratorio, cuando se recibe, entonces se actualiza la muestra correspondiente por su código.
2. Dado un resultado ya cargado, cuando se recibe de nuevo, entonces no se duplica.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- El resultado se asocia a la muestra por su código.
- Un resultado ya procesado no se vuelve a aplicar (idempotencia).
- Los resultados que no se pueden asociar van a una bandeja de errores (HU-F07-2).

**Datos:** Código de muestra, elementos, leyes, fecha de emisión e identificador del resultado en el laboratorio.

**Interfaz y flujo:** No tiene pantalla propia; las leyes aparecen en la muestra y en la pila.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Mecanismo por definir según lo que ofrezca el sistema de laboratorio (API, archivo o vista).
- Procedimiento de integración en el esquema `etl`: `etl.USP_MPB_CARGA_LIMS`; sin linked servers dispersos (se usan synonyms o vistas de integración).
- Tabla de control de procesos para registrar cada ejecución.
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
