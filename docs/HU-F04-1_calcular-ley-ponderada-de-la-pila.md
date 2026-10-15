# HU-F04-1: Calcular ley ponderada de la pila

| Campo | Valor |
|-------|-------|
| Feature | F04 Ley ponderada |
| Ranking WSJF de la feature | #4 (WSJF 7.2) |
| Depende de (feature) | F03 |
| Rol | Supervisor de planta |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |

## Historia

> Como Supervisor de planta, quiero ver la ley ponderada de cada pila, para conocer la calidad del mineral disponible.

## Criterios de aceptación

1. Dada una pila con muestras "Con ley", cuando la consulto, entonces la ley = Σ(ley × tonelaje) / Σ tonelaje de esos volquetes.
2. Dada una pila con muestras pendientes, cuando la consulto, entonces se muestra la ley parcial y el % de tonelaje con ley.

## Supuestos por validar

- Se pondera por tonelaje húmedo (por confirmar con el negocio).

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
