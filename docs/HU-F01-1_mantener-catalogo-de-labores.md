# HU-F01-1: Mantener catálogo de labores

| Campo | Valor |
|-------|-------|
| Feature | F01 Configuración de pilas |
| Ranking WSJF de la feature | #1 (WSJF 15.3) |
| Depende de (feature) | — |
| Rol | Geólogo/Planeamiento |
| Story points | 2 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |

## Historia

> Como Geólogo/Planeamiento, quiero registrar, editar y desactivar labores, para que las pilas y los volquetes se asocien siempre a una labor válida.

## Criterios de aceptación

1. Dado un código de labor nuevo, cuando lo registro con nombre, zona y estado, entonces queda disponible para crear pilas.
2. Dado un código ya existente, cuando intento registrarlo, entonces el sistema lo rechaza por duplicado.
3. Dada una labor con pilas abiertas, cuando intento desactivarla, entonces el sistema lo impide e indica el motivo.

## Supuestos por validar

- Ninguno identificado.

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
