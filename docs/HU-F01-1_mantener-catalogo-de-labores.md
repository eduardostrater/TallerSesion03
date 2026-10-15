# HU-F01-1: Mantener catálogo de labores

| Campo | Valor |
|-------|-------|
| Feature | F01 Configuración de pilas |
| Ranking WSJF de la feature | #1 (WSJF 15.3) |
| Depende de (feature) | — |
| Rol | Geólogo/Planeamiento |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | En revisión |
| Versión | 0.2 |

## Historia

> Como Geólogo/Planeamiento, quiero registrar, editar y desactivar labores con su tipo y zona, para que las pilas y los volquetes se asocien siempre a una labor válida y se pueda reportar la producción por tipo de labor.

## Criterios de aceptación

1. Dado un código de labor nuevo, cuando lo registro con nombre, tipo de labor, zona y estado, entonces queda disponible para crear pilas.
2. Dado un código ya existente, cuando intento registrarlo, entonces el sistema lo rechaza por duplicado.
3. Dada una labor con pilas abiertas, cuando intento desactivarla, entonces el sistema lo impide e indica el motivo.
4. Dado un código de labor con formato inválido (más de 10 caracteres o con caracteres especiales), cuando intento guardarlo, entonces el sistema lo rechaza e indica el formato esperado.
5. Dado el tipo de labor, cuando registro o edito una labor, entonces solo puedo elegirlo de un catálogo (Tajeo, Galería, Chimenea, Rampa, Otro).

## Supuestos por validar

- El código de labor es alfanumérico, en mayúsculas y de hasta 10 caracteres.
- Los tipos de labor listados son una propuesta inicial; Geología debe confirmar el catálogo definitivo.

## Historial de cambios

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
| 0.2 | 2026-10-15 | Se agrega el tipo de labor al enunciado y al criterio 1, la validación del formato del código (criterio 4) y el catálogo de tipos (criterio 5). Story points de 2 a 3. Estado: En revisión. |

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
