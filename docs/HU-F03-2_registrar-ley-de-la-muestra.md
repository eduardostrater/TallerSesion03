# HU-F03-2: Registrar ley de la muestra

| Campo | Valor |
|-------|-------|
| Feature | F03 Muestra por volquete |
| Ranking WSJF de la feature | #3 (WSJF 11.3) |
| Depende de (feature) | F02 |
| Rol | Analista de laboratorio |
| Story points | 3 |
| Incremento | MVP 1: Ley de pila |
| Estado | Borrador |

## Historia

> Como Analista de laboratorio, quiero ingresar la ley de cada muestra por su código, para que se use en el cálculo de la ley de la pila.

## Criterios de aceptación

1. Dado un código de muestra "Pendiente de ley", cuando ingreso la ley [supuesto: Au g/t], entonces la muestra pasa a "Con ley".
2. Dado un valor negativo o no numérico, cuando guardo, entonces el sistema lo rechaza.
3. Dado un código inexistente, cuando lo busco, entonces el sistema informa que no existe.

## Supuestos por validar

- La ley se expresa en Au g/t (confirmar los elementos y las unidades con Laboratorio).

---

Origen: [HU_Propuesta_v0.1.md](../HU_Propuesta_v0.1.md) · Priorización: [WSJF_Propuesta_v0.1.md](../WSJF_Propuesta_v0.1.md)
