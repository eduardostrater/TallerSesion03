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

## Historia

> Como Muestrero, quiero que el sistema genere el código de muestra al tomar la muestra del volquete, para enviarla al laboratorio sin errores de identificación.

## Criterios de aceptación

1. Dado un volquete sin muestra, cuando registro la toma, entonces se genera un código único vinculado 1:1 al volquete.
2. Dado un volquete con muestra, cuando intento registrar otra, entonces el sistema lo impide.
3. La muestra queda en estado "Pendiente de ley".

## Supuestos por validar

- Ninguno identificado.

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
