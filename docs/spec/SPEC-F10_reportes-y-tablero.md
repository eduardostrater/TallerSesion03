# SPEC-F10: Reportes y tablero

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F10-1](../HU-F10-1_ver-tablero-de-stock-y-ley-de-pilas.md), [HU-F10-2](../HU-F10-2_exportar-historico-de-blending.md) |
| Prioridad WSJF | #10 (1.8) |
| Incremento | Evolución |
| Depende de | F04, F06 |
| Versión / Estado | 0.1 / Borrador |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Dar a la jefatura una vista consolidada del inventario de pilas y permitir exportar el histórico de blending para los reportes de gestión.

## 2. Alcance

**Incluye:** tablero de stock y ley de pilas; exportación a Excel del histórico de blending.
**No incluye:** integración con herramientas de BI corporativas (posible evolución).

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Jefe de planta | Consulta el tablero. |
| Supervisor de planta | Exporta el histórico. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F10-01 | El tablero agrupa las pilas por estado (`ABI`, `CER`, `CON`) con su tonelaje, stock y ley ponderada. |
| RN-F10-02 | Las pilas `CON` se muestran solo de los últimos 30 días (configurable). |
| RN-F10-03 | Stock disponible para blending = Σ stock de las pilas `CER`; su ley es la ponderada por stock. |
| RN-F10-04 | La exportación admite un rango de hasta 366 días. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F10-01 | Mostrar indicadores: stock disponible, ley del stock disponible, pilas abiertas y su cobertura media. | HU-F10-1 / CA1 |
| RF-F10-02 | Mostrar tabla de pilas por estado, filtrable por labor y zona. | HU-F10-1 / CA1 |
| RF-F10-03 | Exportar a Excel el detalle por día, tolva y pila con toneladas programadas, reales y ley. | HU-F10-2 / CA1 |
| RF-F10-04 | Rechazar rangos mayores al máximo permitido. | RN-F10-04 |

## 6. Modelo de datos

Sin tablas nuevas.

**Vistas:** `planta.VW_MPB_TABLERO_PILAS` y `planta.VW_MPB_BLENDING_HISTORICO` (fecha, tolva, pila, labor, programado, real, ley).

**Excel:** generado en el servidor con una librería .NET (por ejemplo, ClosedXML); hoja Detalle y hoja Resumen por día.

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| GET | `/api/tablero/pilas?labor=&zona=` | Indicadores y tabla. | — |
| GET | `/api/blending/historico/excel?desde=&hasta=` | Descarga el Excel. | `RANGO_INVALIDO`, `RANGO_EXCEDIDO` |

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `Tablero.razor` | Fila de indicadores, gráfico de barras de stock por estado y labor, tabla filtrable. |
| `HistoricoBlending.razor` | Selector de rango de fechas y botón Exportar. |

## 9. Permisos

Ventana `MPB_TABLERO`: `CON` para Geólogo/Planeamiento, Supervisor, Jefe de planta y Administrador. La exportación requiere `CON` sobre `MPB_BLENDING`.

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F10-01 | 2 pilas `CER` (100 t × 6 g/t, 300 t × 8 g/t de stock). | Stock disponible 400 t; ley 7.5000 g/t. | HU-F10-1 / CA1 |
| CP-F10-02 | Pila consumida hace 45 días. | No aparece en el tablero. | RN-F10-02 |
| CP-F10-03 | Exportar una semana. | Excel con detalle por día, tolva y pila. | HU-F10-2 / CA1 |
| CP-F10-04 | Exportar 400 días. | 422 `RANGO_EXCEDIDO`. | RN-F10-04 |

## 11. Preguntas abiertas

1. ¿Qué indicadores usa hoy la jefatura en sus reportes?
2. ¿El reporte debe seguir un formato corporativo existente?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
