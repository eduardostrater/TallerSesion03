# HU-F09-1: Controlar acceso por ventana

| Campo | Valor |
|-------|-------|
| Feature | F09 Seguridad y roles |
| Ranking WSJF de la feature | #5 (WSJF 7.0) |
| Depende de (feature) | — |
| Rol | Administrador |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |
| Versión | 0.2 |

> **Estructura SDD:** de lo general a lo verificable. Cada capa tiene un responsable y una aprobación propia.

## 1. Necesidad de negocio

*Responsable: Negocio*

Varias áreas registran datos críticos (tonelaje, leyes, blending) y cada una debe limitarse a su función. El negocio necesita controlar quién puede ver y modificar cada ventana.

## 2. Historia de usuario

*Responsable: Negocio + equipo*

> Como Administrador, quiero asignar permisos por ventana y por acción a cada rol, para que cada usuario solo haga lo que le corresponde.

## 3. Criterios de aceptación

*Responsable: Negocio + calidad*

1. Dado un usuario sin permiso sobre una ventana, cuando intenta ingresar, entonces el sistema le niega el acceso.
2. Dado un usuario con permiso de solo consulta, cuando intenta grabar, entonces el servidor rechaza la operación.

## 4. Especificación funcional

*Responsable: Equipo · Aprueba: BP (Business Partner)*

**Reglas de negocio**

- El acceso se define por ventana y por acción (consultar, registrar, modificar, cerrar).
- Los permisos se validan en el servidor, no solo ocultando botones en la interfaz.
- Matriz inicial de roles: Geólogo/Planeamiento, Muestrero, Operador de balanza/despacho, Analista de laboratorio, Supervisor de planta, Jefe de planta, Administrador.

**Datos:** Usuario, rol, ventana, acción y permiso.

**Interfaz y flujo:** Se usa el mantenimiento de permisos del sistema SIA corporativo; no se construye una pantalla propia.

## 5. Especificación técnica preliminar

*Responsable: Desarrollo · Aprueba: Responsable técnico*

- Integración con el control de acceso por ventana SIA (estándar CMH).
- Atributo/filtro de autorización en cada endpoint de la API.
- Registro de ventanas del módulo en el catálogo SIA.
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
