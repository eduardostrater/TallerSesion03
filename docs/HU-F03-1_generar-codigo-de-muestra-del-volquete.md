# HU-F03-1: Generar código de muestra del volquete

| Campo | Valor |
|-------|-------|
| Feature | F03 Muestra por volquete |
| Ranking WSJF de la feature | #3 (WSJF 11.3) |
| Depende de (feature) | F02 |
| Rol | Muestrero |
| Story points | 2 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Las muestras se rotulan a mano y se pierden o se confunden en el laboratorio. El negocio necesita que cada volquete tenga una muestra con un código único y trazable.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Muestrero, quiero que el sistema genere el código de muestra al tomar la muestra del volquete, para enviarla al laboratorio sin errores de identificación.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un volquete sin muestra, cuando registro la toma, entonces se genera un código único vinculado 1:1 al volquete.
2. Dado un volquete con muestra, cuando intento registrar otra, entonces el sistema lo impide.
3. La muestra queda en estado "Pendiente de ley".

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Relación 1:1 entre volquete y muestra.
- El código de muestra lo genera el sistema y es único.
- La muestra nace en estado Pendiente de ley.

**Datos:** Código de muestra, volquete, fecha y hora de toma, muestrero y estado.

**Interfaz y flujo:** Desde el volquete, botón Registrar muestra que genera el código y permite imprimir la etiqueta.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `planta.MPB_MUESTRA` con índice único sobre `NVOLQUETE_ID` y sobre `CMUESTRA_CODIGO`.
- Procedimiento: `planta.USP_MPB_INS_MUESTRA`.
- API: `POST /api/volquetes/{id}/muestra`.
- Front: componente `RegistroMuestra.razor` con vista de impresión de etiqueta.
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
