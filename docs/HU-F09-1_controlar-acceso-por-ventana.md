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

## Historia

> Como Administrador, quiero asignar permisos por ventana y por acción a cada rol, para que cada usuario solo haga lo que le corresponde.

## Criterios de aceptación

1. Dado un usuario sin permiso sobre una ventana, cuando intenta ingresar, entonces el sistema le niega el acceso.
2. Dado un usuario con permiso de solo consulta, cuando intenta grabar, entonces el servidor rechaza la operación.

## Supuestos por validar

- Ninguno identificado.

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
