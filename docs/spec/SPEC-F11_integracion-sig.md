# SPEC-F11: Integración con SIG

| Campo | Valor |
|-------|-------|
| HU cubiertas | [HU-F11-1](../HU-F11-1_importar-poligonos-desde-el-sig.md), [HU-F11-2](../HU-F11-2_visualizar-pilas-en-mapa.md) |
| Prioridad WSJF | #11 (1.2) |
| Incremento | Evolución |
| Depende de | F01 |
| Versión / Estado | 0.1 / Borrador (requiere definir el formato de intercambio) |
| Aprueba funcional | BP · Aprueba técnica: Responsable técnico |

## 1. Objetivo

Importar los polígonos definidos en el SIG para crear o actualizar pilas sin doble digitación, y visualizar las pilas en un mapa con su estado y ley.

## 2. Alcance

**Incluye:** importación de polígonos con vista previa, actualización de geometría, mapa de pilas.
**No incluye:** edición de geometría en este sistema (se edita en el SIG); sincronización en tiempo real.

## 3. Actores

| Actor | Participación |
|-------|---------------|
| Geólogo/Planeamiento | Importa polígonos y consulta el mapa. |
| Jefe de planta / Supervisor | Consultan el mapa. |

## 4. Reglas de negocio

| ID | Regla |
|----|-------|
| RN-F11-01 | Formato de entrada inicial: GeoJSON (`FeatureCollection` de polígonos) con propiedades `poligonoCodigo` y `laborCodigo` (por confirmar con Geología). |
| RN-F11-02 | Sistema de coordenadas: el del SIG corporativo (por confirmar, p. ej. UTM WGS84); se guarda su SRID. |
| RN-F11-03 | Si el polígono ya existe en una pila, se actualiza solo su geometría; si no existe, se crea una pila nueva (reglas de SPEC-F01). |
| RN-F11-04 | Un polígono con labor inexistente o inactiva, o con geometría inválida, se rechaza con su motivo. |
| RN-F11-05 | La importación se confirma después de una vista previa; se aplica en una sola transacción. |
| RN-F11-06 | En el mapa, los polígonos se colorean por estado o por rango de ley, a elección del usuario. |

## 5. Requisitos funcionales

| ID | Requisito | Origen |
|----|-----------|--------|
| RF-F11-01 | Cargar un archivo GeoJSON y ver la vista previa (crear, actualizar, rechazar). | HU-F11-1 / CA1 |
| RF-F11-02 | Confirmar la importación y mostrar el resumen. | HU-F11-1 / CA1 |
| RF-F11-03 | Reportar polígonos con labor inexistente o geometría inválida. | HU-F11-1 / CA2 |
| RF-F11-04 | Mostrar las pilas en un mapa con leyenda por estado o ley. | HU-F11-2 / CA1 |
| RF-F11-05 | Al seleccionar un polígono, mostrar código, labor, tonelaje, ley y estado. | HU-F11-2 / CA1 |

## 6. Modelo de datos

Columnas nuevas en `planta.MPB_PILA`:

| Columna | Tipo | Nulo | Descripción |
|---------|------|------|-------------|
| `XPOLIGONO_GEOMETRIA` | GEOMETRY | Sí | Geometría del polígono. |
| `NSRID_CODIGO` | INT | Sí | Sistema de coordenadas. |
| `DFECHA_IMPORTACION` | DATETIME2 | Sí | Última importación. |

Índice espacial sobre `XPOLIGONO_GEOMETRIA` si el volumen lo justifica.

**Procedimiento:** `planta.USP_MPB_IMPORTA_POLIGONOS` (recibe un parámetro de tabla con los polígonos validados).

## 7. Contrato de API

| Método | Ruta | Descripción | Errores de negocio |
|--------|------|-------------|--------------------|
| POST | `/api/sig/importar/vista-previa` | Recibe el GeoJSON y devuelve la clasificación. | `ARCHIVO_INVALIDO` |
| POST | `/api/sig/importar/confirmar` | Aplica la importación. | `VISTA_PREVIA_VENCIDA` |
| GET | `/api/sig/pilas?estado=` | Pilas en formato GeoJSON. | — |

Ejemplo de respuesta de vista previa:

```json
{ "token": "b7f3...", "crear": 4, "actualizar": 9, "rechazar": 1,
  "rechazos": [ { "poligonoCodigo": "POL-2026-131", "motivo": "LABOR_INEXISTENTE" } ] }
```

## 8. Interfaz

| Pantalla | Contenido |
|----------|-----------|
| `ImportarPoligonos.razor` | Carga de archivo, tabla de vista previa con filtros por acción, botón Confirmar y resumen final. |
| `MapaPilas.razor` | Mapa interactivo (librería por definir, p. ej. Leaflet vía JS interop), selector de coloreado, leyenda y panel de detalle. |

## 9. Permisos

Ventana `MPB_SIG`: `CON` para Geólogo/Planeamiento, Supervisor, Jefe de planta y Administrador; `REG` (importar) para Geólogo/Planeamiento.

## 10. Casos de prueba

| ID | Caso | Resultado esperado | Traza |
|----|------|--------------------|-------|
| CP-F11-01 | Importar GeoJSON con 3 polígonos nuevos y 2 existentes. | Vista previa: 3 crear, 2 actualizar; tras confirmar, pilas creadas y geometrías actualizadas. | HU-F11-1 / CA1 |
| CP-F11-02 | Polígono con labor inexistente. | Rechazado con `LABOR_INEXISTENTE`. | HU-F11-1 / CA2 |
| CP-F11-03 | Polígono con geometría inválida (auto-intersección). | Rechazado con `GEOMETRIA_INVALIDA`. | RN-F11-04 |
| CP-F11-04 | Abrir el mapa y seleccionar una pila. | Panel con código, labor, tonelaje, ley y estado. | HU-F11-2 / CA1 |

## 11. Preguntas abiertas

1. ¿Qué herramienta SIG se usa y qué formatos puede exportar?
2. ¿Cuál es el sistema de coordenadas oficial?
3. ¿Se necesita un mapa base (imagen satelital o plano de la mina)?

## Historial

| Versión | Fecha | Cambio |
|---------|-------|--------|
| 0.1 | 2026-10-15 | Versión inicial. |
