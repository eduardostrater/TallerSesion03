# HU-F05-2: Registrar carga real de pila a tolva

| Campo | Valor |
|-------|-------|
| Feature | F05 Blending a tolvas |
| Ranking WSJF de la feature | #7 (WSJF 5.2) |
| Depende de (feature) | F01, F04 |
| Rol | Operador de despacho |
| Story points | 3 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Lo que realmente se envía de cada pila a planta no siempre coincide con lo programado y no se descuenta del stock. El negocio necesita registrar los envíos reales para mantener el inventario de pilas al día.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Operador de despacho, quiero registrar las toneladas realmente enviadas de cada pila a cada tolva, para descontar el stock y alimentar la carga del día.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un envío registrado, cuando guardo, entonces se descuenta del stock de la pila origen.
2. Dada una pila que llega a stock 0, entonces pasa a estado "Consumida".

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Cada envío descuenta su tonelaje del stock de la pila de origen.
- No se puede enviar más que el stock de la pila.
- Cuando el stock llega a 0, la pila pasa a estado Consumida.
- No se registran envíos en días cerrados.

**Datos:** Fecha y hora, pila, tolva, toneladas enviadas y operador.

**Interfaz y flujo:** Formulario de envío con pila, tolva y toneladas; muestra el stock restante.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `planta.MPB_BLENDING_ENVIO`.
- Procedimiento: `planta.USP_MPB_INS_ENVIO` (descuento de stock atómico para evitar que dos envíos simultáneos dejen saldo negativo).
- API: `POST /api/blending/envios`.
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
