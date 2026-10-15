# SPEC-F04: Ley ponderada de pila

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F04-1](../HU-F04-1_calcular-ley-ponderada-de-la-pila.md), [HU-F04-2](../HU-F04-2_cerrar-pila-con-ley-final.md) |
| Prioridad WSJF | #4 (7.2) |
| Incremento | MVP 1: Ley de pila |
| Depende de | F03 |
| Habilita | F05, F10 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Calcular en todo momento la ley ponderada de cada pila y cerrarla con una ley y un tonelaje definitivos para que pueda usarse en el blending.

## 2. Alcance

**Incluye:** cálculo y persistencia de la ley ponderada, indicador de cobertura, cierre de pila.
**No incluye:** reapertura de pilas cerradas (fuera de alcance del MVP).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Supervisor de planta | Consulta la ley de las pilas. |
| Geólogo/Planeamiento | Cierra pilas. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F04-01 | Ley de pila = Σ(ley × tonelaje) / Σ tonelaje, considerando solo volquetes no anulados con muestra en estado `LEY`. |
| RN-F04-02 | Cobertura % = tonelaje con ley / tonelaje total × 100. |
| RN-F04-03 | Si la cobertura es 0 %, la ley de la pila es nula (se muestra "Sin ley"). |
| RN-F04-04 | El cálculo se ejecuta siempre en el servidor y se persiste en `MPB_PILA` cada vez que cambia una ley o un tonelaje. |
| RN-F04-05 | Solo se cierra una pila `ABI` con cobertura del 100 % y al menos un volquete. |
| RN-F04-06 | Al cerrar se fijan la ley final, el tonelaje final, el stock inicial (= tonelaje final), la fecha y el usuario de cierre. |
| RN-F04-07 | Una pila `CER` no admite volquetes, correcciones de tonelaje ni de ley (candado de negocio). |
| RN-F04-08 | El tonelaje usado es el húmedo registrado en balanza (supuesto por confirmar). |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F04-01 | Mostrar la ley ponderada de cada pila en la grilla y en el detalle. | HU-F04-1 / CA1 |
| RF-F04-02 | Mostrar la ley parcial y el % de cobertura cuando hay muestras pendientes. | HU-F04-1 / CA2 |
| RF-F04-03 | Mostrar en el detalle la contribución de cada volquete (tonelaje, ley, estado). | HU-F04-1 |
| RF-F04-04 | Cerrar una pila con cobertura del 100 %. | HU-F04-2 / CA1 |
| RF-F04-05 | Rechazar el cierre si hay muestras pendientes, indicando cuántas faltan. | HU-F04-2 / CA2 |
| RF-F04-06 | Bloquear altas y cambios sobre pilas cerradas. | HU-F04-2 / CA3 |

## 6. Modelo de datos

Usa las columnas `NLEY_AU`, `NTONELAJE_CON_LEY`, `NSTOCK_CANTIDAD`, `DFECHA_CIERRE` y `CUSUARIO_CIERRE` de `planta.MPB_PILA` (definidas en SPEC-F01).

**Procedimientos:**

- `planta.USP_MPB_RECALCULA_LEY_PILA (@pi_PilaId)`: recalcula y persiste la ley y la cobertura; lo llaman los procedimientos de volquete y de ley dentro de su transacción.
- `planta.USP_MPB_CIERRA_PILA (@pi_PilaId, @pi_Usuario, @po_Retorno, @po_Mensaje)`: bloquea la fila, revalida RN-F04-05 y cierra.

**Vista:** `planta.VW_MPB_PILA_LEY` (pila, tonelaje, tonelaje con ley, cobertura, ley, muestras pendientes).

Cálculo de referencia:

```sql
SELECT  v.NPILA_ID,
        SUM(v.NTONELAJE_CANTIDAD)                                   AS NTONELAJE_CANTIDAD,
        SUM(CASE WHEN m.CMUESTRA_ESTADO = 'LEY' THEN v.NTONELAJE_CANTIDAD ELSE 0 END) AS NTONELAJE_CON_LEY,
        SUM(CASE WHEN m.CMUESTRA_ESTADO = 'LEY' THEN v.NTONELAJE_CANTIDAD * m.NLEY_AU END)
          / NULLIF(SUM(CASE WHEN m.CMUESTRA_ESTADO = 'LEY' THEN v.NTONELAJE_CANTIDAD ELSE 0 END), 0) AS NLEY_AU
FROM    planta.MPB_VOLQUETE v
LEFT JOIN planta.MPB_MUESTRA m ON m.NVOLQUETE_ID = v.NVOLQUETE_ID
WHERE   v.NPILA_ID = @pi_PilaId
  AND   v.BANULADO = 0
GROUP BY v.NPILA_ID;
```

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| GET | `/api/pilas/{id}/ley` | Ley, cobertura y detalle por volquete. | `PILA_INEXISTENTE` |
| POST | `/api/pilas/{id}/cerrar` | Cierra la pila. | `PILA_NO_ABIERTA`, `COBERTURA_INCOMPLETA`, `PILA_SIN_VOLQUETES` |

Ejemplo de rechazo de cierre:

```json
// Respuesta 409
{ "codigo": "COBERTURA_INCOMPLETA",
  "mensaje": "La pila tiene 2 muestras pendientes de ley (cobertura 91.4 %).",
  "detalle": { "pendientes": 2, "coberturaPct": 91.4 } }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `Pilas.razor` | Columnas de ley y cobertura con barra de progreso. |
| `PilaDetalle.razor` | Resumen (tonelaje, ley, cobertura) y tabla de volquetes con su ley; botón Cerrar pila habilitado solo con cobertura 100 % y confirmación previa. |

## 9. Permisos

| Acción | Geólogo/Plan. | Supervisor | Jefe de planta |
|--------|:---:|:---:|:---:|
| Consultar ley | ✔ | ✔ | ✔ |
| Cerrar pila | ✔ | | |

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F04-01 | Pila con 3 volquetes (30 t × 5 g/t, 20 t × 8 g/t, 50 t × 6 g/t). | Ley = 6.1000 g/t; cobertura 100 %. | HU-F04-1 / CA1 |
| CP-F04-02 | Misma pila con la tercera muestra pendiente. | Ley parcial = 6.2000 g/t; cobertura 50 %. | HU-F04-1 / CA2 |
| CP-F04-03 | Pila sin muestras con ley. | Ley nula ("Sin ley"); cobertura 0 %. | RN-F04-03 |
| CP-F04-04 | Corregir tonelaje de un volquete con ley. | Ley de pila recalculada. | RN-F04-04 |
| CP-F04-05 | Cerrar pila con cobertura 100 %. | Estado `CER`; ley, tonelaje y stock fijados. | HU-F04-2 / CA1 |
| CP-F04-06 | Cerrar pila con 2 muestras pendientes. | 409 `COBERTURA_INCOMPLETA` con 2 pendientes. | HU-F04-2 / CA2 |
| CP-F04-07 | Registrar volquete o corregir ley en pila cerrada. | 409 `PILA_NO_ABIERTA`. | HU-F04-2 / CA3 |

## 11. Preguntas abiertas

1. ¿Se pondera con tonelaje húmedo o seco? Si es seco, ¿quién registra la humedad y por volquete o por pila?
2. ¿Se necesita reabrir una pila cerrada? Si es así, ¿con qué aprobación?
3. ¿Hay una tolerancia de cobertura menor a 100 % aceptable para cerrar?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
