# HU-F02-2: Consultar y corregir volquetes del turno

| Campo | Valor |
|-------|-------|
| Feature | F02 Registro de volquetes |
| Ranking WSJF de la feature | #2 (WSJF 13.0) |
| Depende de (feature) | F01 |
| Rol | Supervisor de planta |
| Story points | 2 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |

## Historia

> Como Supervisor de planta, quiero listar los volquetes del turno y corregir datos mal registrados, para asegurar que el tonelaje de la pila sea confiable.

## Criterios de aceptación

1. Dado un turno, cuando filtro por fecha y pila, entonces veo los volquetes con su tonelaje y el estado de su muestra.
2. Dado un volquete de una pila abierta, cuando corrijo el tonelaje, entonces se recalcula el acumulado y se registra la auditoría.

## Supuestos por validar

- Ninguno identificado.

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
