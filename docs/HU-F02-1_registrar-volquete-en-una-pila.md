# HU-F02-1: Registrar volquete en una pila

| Campo | Valor |
|-------|-------|
| Feature | F02 Registro de volquetes |
| Ranking WSJF de la feature | #2 (WSJF 13.0) |
| Depende de (feature) | F01 |
| Rol | Operador de balanza |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

El tonelaje que ingresa a cada pila se lleva en registros manuales de balanza. El negocio necesita registrar cada volquete en su pila para conocer el tonelaje real y poder ponderar la ley.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Operador de balanza, quiero registrar cada volquete con placa, tonelaje, fecha/hora y pila, para tener el detalle de lo que ingresa a cada pila.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una pila "Abierta", cuando registro un volquete con todos sus datos, entonces queda asociado a la pila y se actualiza su tonelaje acumulado.
2. Dada una pila "Cerrada", cuando intento asignarle un volquete, entonces el sistema lo rechaza.
3. Dado un tonelaje menor o igual a 0 o fuera del rango configurado, cuando guardo, entonces el sistema muestra un error de validación.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Solo se pueden asignar volquetes a pilas en estado Abierta.
- El tonelaje debe ser mayor a 0 y estar dentro de un rango configurable por tipo de volquete.
- Cada registro actualiza el tonelaje acumulado y el número de volquetes de la pila.
- La fecha y hora de pesaje no puede ser futura.

**Datos:** Placa, tonelaje, fecha y hora de pesaje, pila, labor (derivada de la pila), turno y operador.

**Interfaz y flujo:** Formulario de registro rápido orientado a balanza: placa, tonelaje y pila; muestra el acumulado de la pila tras grabar.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `planta.MPB_VOLQUETE` con FK a `planta.MPB_PILA`.
- Procedimiento: `planta.USP_MPB_INS_VOLQUETE`; valida el estado de la pila y actualiza el acumulado en la misma transacción.
- API: `POST /api/pilas/{id}/volquetes`.
- Front: página Blazor `RegistroVolquete.razor`.
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
