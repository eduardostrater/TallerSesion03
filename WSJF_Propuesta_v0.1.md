# Priorización WSJF: Muestreo de Pilas y Blending (propuesta v0.1)

## 1. Prompt optimizado (ingeniería de prompts)

Esta es la versión estructurada del pedido original. Puede reutilizarse en las siguientes iteraciones.

```text
[ROL]
Actúa como Product Owner / RTE con experiencia en SAFe y en procesos de planta
metalúrgica (muestreo, control de leyes y blending de mineral).

[CONTEXTO]
El proyecto digitaliza tres procesos encadenados:
1. Programa de muestreo SIG: identifica las cargas de cada labor y las configura
   como un polígono. Cada polígono = una pila.
2. Registro de muestras: se crea/registra cada volquete asociado al polígono.
   Por cada volquete se toma una muestra. El ponderado de todas las muestras
   da la ley de la pila.
3. Registro de blending: carga lo que el programa envía de cada pila a las tolvas;
   la suma de las tolvas es la carga del día.

[TAREA]
Descompón el alcance en features (épicas pequeñas) y priorízalas con WSJF:
  CoD  = Valor de negocio (BV) + Criticidad temporal (TC) + Reducción de riesgo /
         Habilitación de oportunidad (RR|OE)
  WSJF = CoD / Tamaño del trabajo (JS)

[RESTRICCIONES]
- Escala Fibonacci modificada: 1, 2, 3, 5, 8, 13, 20.
- Puntuación relativa: en cada columna, la feature más baja vale 1.
- Señala dependencias que obliguen a alterar el orden puro de WSJF.
- Declara supuestos; no inventes datos de negocio.

[FORMATO DE SALIDA]
1. Tabla de features (ID, nombre, descripción, dependencia).
2. Tabla WSJF ordenada de mayor a menor.
3. Backlog sugerido por incrementos (MVP / siguientes).
4. Supuestos y preguntas abiertas para validar con el negocio.
```

## 2. Features identificadas

| ID  | Feature | Descripción | Depende de |
|-----|---------|-------------|------------|
| F01 | Configuración de pilas (polígonos) | Catálogo de labores y alta de polígono/pila por labor (manual en el MVP) | — |
| F02 | Registro de volquetes | Alta de cada volquete con tonelaje, labor, fecha/hora y pila asignada | F01 |
| F03 | Registro de muestra por volquete | Código de muestra 1:1 con el volquete, estado de la muestra | F02 |
| F04 | Ley ponderada de pila | Ley = Σ(ley × tonelaje) / Σ tonelaje, recalculada al ingresar resultados | F03 |
| F05 | Registro de blending a tolvas | Toneladas que salen de cada pila hacia cada tolva según el programa | F01, F04 |
| F06 | Carga del día | Consolidado diario: suma de tolvas, tonelaje total y ley de cabeza ponderada | F05 |
| F07 | Integración con laboratorio | Recepción automática de leyes desde el sistema de laboratorio | F03 |
| F08 | Trazabilidad labor → pila → volquete → tolva | Consulta del origen completo de la carga del día | F05 |
| F09 | Seguridad y roles | Control de acceso por ventana (estándar SIA de CMH) y auditoría | — |
| F10 | Reportes / tablero | Stock y ley de pilas, histórico de blending | F04, F06 |
| F11 | Integración geoespacial con SIG | Importar polígonos reales desde la herramienta SIG | F01 |

## 3. Matriz WSJF

| Rank | ID  | Feature | BV | TC | RR\|OE | **CoD** | JS | **WSJF** |
|-----:|-----|---------|---:|---:|------:|--------:|---:|---------:|
| 1  | F01 | Configuración de pilas            | 13 | 13 | 20 | 46 | 3  | **15.3** |
| 2  | F02 | Registro de volquetes             | 13 | 13 | 13 | 39 | 3  | **13.0** |
| 3  | F03 | Registro de muestra por volquete  | 13 | 8  | 13 | 34 | 3  | **11.3** |
| 4  | F04 | Ley ponderada de pila             | 20 | 8  | 8  | 36 | 5  | **7.2**  |
| 5  | F09 | Seguridad y roles                 | 5  | 8  | 8  | 21 | 3  | **7.0**  |
| 6  | F06 | Carga del día                     | 8  | 5  | 3  | 16 | 3  | **5.3**  |
| 7  | F05 | Registro de blending a tolvas     | 13 | 5  | 8  | 26 | 5  | **5.2**  |
| 8  | F08 | Trazabilidad completa             | 8  | 3  | 8  | 19 | 5  | **3.8**  |
| 9  | F07 | Integración con laboratorio       | 13 | 5  | 13 | 31 | 13 | **2.4**  |
| 10 | F10 | Reportes / tablero                | 5  | 2  | 2  | 9  | 5  | **1.8**  |
| 11 | F11 | Integración geoespacial con SIG   | 8  | 3  | 5  | 16 | 13 | **1.2**  |

**Ajuste por dependencias:** F06 tiene un WSJF ligeramente mayor que F05, pero depende de él. Por eso se ejecutan juntos, en el orden F05 → F06.

## 4. Backlog sugerido por incrementos

| Incremento | Features | Resultado para el negocio |
|------------|----------|---------------------------|
| **MVP 1: Ley de pila** | F01, F02, F03, F04, F09 | Se conoce la ley ponderada de cada pila con registro manual de leyes |
| **MVP 2: Blending** | F05, F06, F08 | Se registra la carga a tolvas y se obtiene la carga del día con trazabilidad |
| **Evolución** | F07, F10, F11 | Menos digitación, visibilidad gerencial e integración real con SIG |

## 5. Supuestos

- En el MVP, el polígono se registra como un catálogo manual; la geometría real del SIG queda para F11.
- La relación volquete–muestra es 1:1.
- La ley de pila se pondera por tonelaje húmedo o seco (**por confirmar**).
- Las puntuaciones son una primera estimación del equipo técnico y deben calibrarse con el negocio (Planta, Geología, Laboratorio).

## 6. Preguntas abiertas para la sesión de validación

1. ¿Qué elementos se reportan en la ley (Au, Ag, otros) y en qué unidades?
2. ¿El ponderado usa tonelaje húmedo o seco? ¿Quién aporta la humedad?
3. ¿Una pila puede recibir volquetes de más de una labor?
4. ¿El "programa" de blending es un plan previo (recomendado) o lo que se registra en la operación real?
5. ¿Existe hoy un sistema de laboratorio con el que haya que integrarse? ¿Qué interfaz ofrece?
6. ¿Hay alguna fecha regulatoria u operativa que eleve la criticidad temporal de algún feature?
