# SPEC-F08: Trazabilidad

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F08-1](../HU-F08-1_rastrear-origen-de-la-carga-del-dia.md), [HU-F08-2](../HU-F08-2_rastrear-destino-de-una-pila.md) |
| Prioridad WSJF | #8 (3.8) |
| Incremento | MVP 2: Blending |
| Depende de | F05, F06 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Permitir rastrear la carga del día hasta su origen (labor, volquete y muestra) y el destino de cada pila, para explicar desviaciones de ley y conciliar la producción por labor.

## 2. Alcance

**Incluye:** consulta de origen desde la carga del día y consulta de destino desde la pila. Solo lectura.
**No incluye:** exportación (SPEC-F10).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Jefe de planta | Rastrea el origen de la carga. |
| Geólogo/Planeamiento | Rastrea el destino de una pila. |
| Supervisor de planta | Consulta ambos. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F08-01 | Cadena de origen: carga del día → tolva → envío → pila → volquete → muestra → labor. |
| RN-F08-02 | Los envíos y volquetes anulados no se muestran por defecto; hay un filtro para incluirlos. |
| RN-F08-03 | Aporte de una labor a la carga del día = Σ t enviadas de sus pilas en la fecha. |
| RN-F08-04 | Saldo de pila = tonelaje final − Σ t enviadas no anuladas (coincide con `NSTOCK_CANTIDAD`). |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F08-01 | Navegar desde la carga del día hasta cada volquete y su muestra. | HU-F08-1 / CA1 |
| RF-F08-02 | Mostrar el aporte por labor (t y % de la carga, ley media). | HU-F08-1 |
| RF-F08-03 | Mostrar los envíos de una pila por fecha y tolva, con el saldo. | HU-F08-2 / CA1 |

## 6. Modelo de datos

Sin tablas nuevas.

**Vistas:**

- `planta.VW_MPB_TRAZABILIDAD_ORIGEN`: fecha, tolva, envío, pila, labor, t enviadas, ley.
- `planta.VW_MPB_PILA_DESTINO`: pila, fecha, tolva, t enviadas, saldo acumulado.

Los volquetes de una pila se obtienen bajo demanda desde `VW_MPB_VOLQUETE_TURNO` (SPEC-F02), para no cargar todo el árbol de una vez.

## 7. Contrato de API

| Método | Ruta | Descripción |
|--------|------|-------------|
| GET | `/api/trazabilidad/carga-dia/{fecha}` | Tolvas, envíos y pilas de la fecha, con aporte por labor. |
| GET | `/api/trazabilidad/pilas/{id}/volquetes` | Volquetes y muestras de la pila (paginado). |
| GET | `/api/pilas/{id}/destino` | Envíos de la pila y saldo. |

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `TrazabilidadOrigen.razor` | Árbol expandible fecha → tolva → pila → volquete → muestra; panel lateral de aporte por labor. |
| Pestaña Destino (en `PilaDetalle.razor`) | Tabla de envíos por fecha y tolva con saldo acumulado. |

## 9. Permisos

Ventana `MPB_TRAZABILIDAD` con acción `CON` para Geólogo/Planeamiento, Supervisor, Jefe de planta y Administrador.

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F08-01 | Abrir la carga de un día con 2 tolvas y 3 pilas. | Se llega hasta cada volquete y su muestra. | HU-F08-1 / CA1 |
| CP-F08-02 | Aporte por labor. | La suma de aportes es igual a la carga del día. | RN-F08-03 |
| CP-F08-03 | Destino de una pila con 3 envíos. | 3 filas; saldo final igual al stock de la pila. | HU-F08-2 / CA1 |
| CP-F08-04 | Pila con un envío anulado. | No aparece salvo que se active el filtro. | RN-F08-02 |

## 11. Preguntas abiertas

1. ¿Se necesita la trazabilidad inversa desde una labor (todas sus pilas y destinos)?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
