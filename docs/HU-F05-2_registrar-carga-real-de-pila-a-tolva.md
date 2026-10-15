# HU-F05-2: Registrar carga real de pila a tolva

| Campo | Valor |
|-------|-------|
| Feature | F05 Blending a tolvas |
| Ranking WSJF de la feature | #7 (WSJF 5.2) |
| Depende de (feature) | F01, F04 |
| Rol | Operador de despacho |
| Story points | 3 |
| Incremento | MVP 2: Blending |
| Estado | Borrador |

## Historia

> Como Operador de despacho, quiero registrar las toneladas realmente enviadas de cada pila a cada tolva, para descontar el stock y alimentar la carga del día.

## Criterios de aceptación

1. Dado un envío registrado, cuando guardo, entonces se descuenta del stock de la pila origen.
2. Dada una pila que llega a stock 0, entonces pasa a estado "Consumida".

## Supuestos por validar

- Ninguno identificado.

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
