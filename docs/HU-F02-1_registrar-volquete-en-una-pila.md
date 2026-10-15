# HU-F02-1: Registrar volquete en una pila

| Campo | Valor |
|-------|-------|
| Feature | F02 Registro de volquetes |
| Ranking WSJF de la feature | #2 (WSJF 13.0) |
| Depende de (feature) | F01 |
| Rol | Operador de balanza |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |

## Historia

> Como Operador de balanza, quiero registrar cada volquete con placa, tonelaje, fecha/hora y pila, para tener el detalle de lo que ingresa a cada pila.

## Criterios de aceptación

1. Dada una pila "Abierta", cuando registro un volquete con todos sus datos, entonces queda asociado a la pila y se actualiza su tonelaje acumulado.
2. Dada una pila "Cerrada", cuando intento asignarle un volquete, entonces el sistema lo rechaza.
3. Dado un tonelaje menor o igual a 0 o fuera del rango configurado, cuando guardo, entonces el sistema muestra un error de validación.

## Supuestos por validar

- Ninguno identificado.

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
