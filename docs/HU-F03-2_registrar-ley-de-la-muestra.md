# HU-F03-2: Registrar ley de la muestra

| Campo | Valor |
|-------|-------|
| Feature | F03 Muestra por volquete |
| Ranking WSJF de la feature | #3 (WSJF 11.3) |
| Depende de (feature) | F02 |
| Rol | Analista de laboratorio |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Los resultados del laboratorio llegan en hojas sueltas y se transcriben varias veces. El negocio necesita registrar la ley de cada muestra una sola vez, vinculada a su volquete.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Analista de laboratorio, quiero ingresar la ley de cada muestra por su código, para que se use en el cálculo de la ley de la pila.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un código de muestra "Pendiente de ley", cuando ingreso la ley [supuesto: Au g/t], entonces la muestra pasa a "Con ley".
2. Dado un valor negativo o no numérico, cuando guardo, entonces el sistema lo rechaza.
3. Dado un código inexistente, cuando lo busco, entonces el sistema informa que no existe.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Solo se registra la ley de muestras en estado Pendiente de ley.
- La ley debe ser numérica y mayor o igual a 0.
- Al registrar la ley, la muestra pasa a estado Con ley y se recalcula la ley de la pila (HU-F04-1).
- Una ley ya registrada solo se corrige con motivo y queda auditada.

**Datos:** Código de muestra, ley por elemento (inicialmente Au g/t), fecha de resultado y analista.

**Interfaz y flujo:** Búsqueda por código de muestra (compatible con lector de código de barras) y campo de ley.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Procedimiento: `planta.USP_MPB_REGISTRA_LEY` (actualiza la muestra y dispara el recálculo de la pila).
- Las columnas de ley usan `DECIMAL(10,4)`, nunca `FLOAT`.
- API: `PUT /api/muestras/{codigo}/ley`.
- Front: página Blazor `RegistroLey.razor`.
- Stack de referencia: Blazor + ASP.NET Core Web API + Dapper sobre SQL Server, según el estándar de desarrollo y de base de datos de CMH.
- Nombres preliminares: el esquema `planta` y el prefijo `MPB` (Muestreo de Pilas y Blending) están por confirmar con el DBA.

## Supuestos por validar

- La ley se expresa en Au g/t (confirmar los elementos y las unidades con Laboratorio).

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
