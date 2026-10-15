# HU-F01-2: Crear pila (polígono) por labor

| Campo | Valor |
|-------|-------|
| Feature | F01 Configuración de pilas |
| Ranking WSJF de la feature | #1 (WSJF 15.3) |
| Depende de (feature) | — |
| Rol | Geólogo/Planeamiento |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Las cargas de cada labor se acumulan en pilas, pero no existe un registro formal de qué pila corresponde a qué labor. El negocio necesita identificar cada pila (polígono) para saber qué mineral contiene.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Geólogo/Planeamiento, quiero crear una pila asociada a una labor con su código de polígono, para identificar a qué pila pertenece cada carga.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dada una labor activa, cuando creo una pila, entonces se genera con un código único y en estado "Abierta".
2. Dada una pila abierta, cuando la consulto, entonces veo la labor, la fecha de creación y su número de volquetes (inicialmente 0).

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- Una pila pertenece a una sola labor activa.
- El código de pila es único y lo genera el sistema (formato propuesto: `<labor>-<AAAAMMDD>-<correlativo>`).
- Una pila nace en estado Abierta, con tonelaje y número de volquetes en 0.
- Estados de la pila: Abierta → Cerrada → Consumida.

**Datos:** Código de pila, labor, código de polígono, fecha de creación, estado, tonelaje acumulado y número de volquetes.

**Interfaz y flujo:** Ventana de pilas con filtro por labor y estado; botón Nueva pila que pide la labor y el código de polígono.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Tabla: `planta.MPB_PILA` con FK `FK_MPB_PILA_MPB_LABOR`. Campo de estado `CPILA_ESTADO` (ABI, CER, CON).
- Procedimiento: `planta.USP_MPB_INS_PILA` (genera el código dentro de la transacción para evitar duplicados).
- API: `GET/POST /api/pilas`.
- Front: página Blazor `Pilas.razor`.
- Stack de referencia: Blazor + ASP.NET Core Web API + Dapper sobre SQL Server, según el estándar de desarrollo y de base de datos de CMH.
- Nombres preliminares: el esquema `planta` y el prefijo `MPB` (Muestreo de Pilas y Blending) están por confirmar con el DBA.

## Supuestos por validar

- Una pila pertenece a una sola labor.

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
