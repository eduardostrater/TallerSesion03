# Historias de Usuario: Muestreo de Pilas y Blending (propuesta v0.1)

Fuente: [WSJF_Propuesta_v0.1.md](WSJF_Propuesta_v0.1.md). Se generan 2 HU por cada una de las 11 features, ordenadas según el ranking WSJF.

## 1. Prompt utilizado

```text
[ROL]
Actúa como Product Owner con experiencia en SAFe, redacción de historias de
usuario (INVEST) y procesos de planta metalúrgica (muestreo, leyes, blending).

[CONTEXTO]
Se tiene un backlog de 11 features priorizadas con WSJF (archivo
WSJF_Propuesta_v0.1.md) para digitalizar:
1. Programa de muestreo SIG: cargas por labor configuradas como polígono = pila.
2. Registro de muestras: un volquete por carga, una muestra por volquete;
   el ponderado de las muestras da la ley de la pila.
3. Registro de blending: lo que se envía de cada pila a las tolvas; la suma de
   tolvas es la carga del día.

[TAREA]
Por cada feature, genera exactamente 2 historias de usuario.

[RESTRICCIONES]
- Formato: "Como <rol>, quiero <acción>, para <beneficio>".
- Roles válidos: Geólogo/Planeamiento, Muestrero, Operador de balanza/despacho,
  Analista de laboratorio, Supervisor de planta, Jefe de planta, Administrador.
- Cada HU debe cumplir INVEST: pequeña, independiente y verificable.
- De 2 a 4 criterios de aceptación por HU en formato Dado/Cuando/Entonces.
- Mantén el orden y el ID de la feature (F01..F11); ID de HU: HU-Fxx-n.
- Estima en story points (Fibonacci) y asigna el incremento (MVP1, MVP2, Evol.).
- No inventes reglas de negocio: márcalas como "[supuesto]".

[FORMATO DE SALIDA]
1. Tabla resumen (ID, feature, título, rol, SP, incremento).
2. Detalle por HU: enunciado y criterios de aceptación.
```

## 2. Resumen

| ID | Feature | Título | Rol | SP | Incremento |
|----|---------|--------|-----|---:|-----------|
| HU-F01-1 | F01 Configuración de pilas | Mantener catálogo de labores | Geólogo/Planeamiento | 2 | MVP1 |
| HU-F01-2 | F01 Configuración de pilas | Crear pila (polígono) por labor | Geólogo/Planeamiento | 3 | MVP1 |
| HU-F02-1 | F02 Registro de volquetes | Registrar volquete en una pila | Operador de balanza | 3 | MVP1 |
| HU-F02-2 | F02 Registro de volquetes | Consultar y corregir volquetes del turno | Supervisor de planta | 2 | MVP1 |
| HU-F03-1 | F03 Muestra por volquete | Generar código de muestra del volquete | Muestrero | 2 | MVP1 |
| HU-F03-2 | F03 Muestra por volquete | Registrar ley de la muestra | Analista de laboratorio | 3 | MVP1 |
| HU-F04-1 | F04 Ley ponderada | Calcular ley ponderada de la pila | Supervisor de planta | 3 | MVP1 |
| HU-F04-2 | F04 Ley ponderada | Cerrar pila con ley final | Geólogo/Planeamiento | 2 | MVP1 |
| HU-F09-1 | F09 Seguridad y roles | Controlar acceso por ventana | Administrador | 3 | MVP1 |
| HU-F09-2 | F09 Seguridad y roles | Auditar cambios en registros críticos | Jefe de planta | 2 | MVP1 |
| HU-F05-1 | F05 Blending a tolvas | Registrar programa de blending del día | Supervisor de planta | 3 | MVP2 |
| HU-F05-2 | F05 Blending a tolvas | Registrar carga real de pila a tolva | Operador de despacho | 3 | MVP2 |
| HU-F06-1 | F06 Carga del día | Consolidar carga del día | Supervisor de planta | 3 | MVP2 |
| HU-F06-2 | F06 Carga del día | Comparar programado vs. real | Jefe de planta | 2 | MVP2 |
| HU-F08-1 | F08 Trazabilidad | Rastrear origen de la carga del día | Jefe de planta | 3 | MVP2 |
| HU-F08-2 | F08 Trazabilidad | Rastrear destino de una pila | Geólogo/Planeamiento | 2 | MVP2 |
| HU-F07-1 | F07 Integración laboratorio | Recibir leyes automáticamente | Analista de laboratorio | 8 | Evol. |
| HU-F07-2 | F07 Integración laboratorio | Gestionar errores de integración | Administrador | 3 | Evol. |
| HU-F10-1 | F10 Reportes | Ver tablero de stock y ley de pilas | Jefe de planta | 5 | Evol. |
| HU-F10-2 | F10 Reportes | Exportar histórico de blending | Supervisor de planta | 2 | Evol. |
| HU-F11-1 | F11 Integración SIG | Importar polígonos desde el SIG | Geólogo/Planeamiento | 8 | Evol. |
| HU-F11-2 | F11 Integración SIG | Visualizar pilas en mapa | Geólogo/Planeamiento | 5 | Evol. |

**Total:** MVP1 = 25 SP · MVP2 = 16 SP · Evolución = 31 SP

## 3. Detalle

### F01: Configuración de pilas (polígonos)

**HU-F01-1: Mantener catálogo de labores**
Como Geólogo/Planeamiento, quiero registrar, editar y desactivar labores, para que las pilas y los volquetes se asocien siempre a una labor válida.
- Dado un código de labor nuevo, cuando lo registro con nombre, zona y estado, entonces queda disponible para crear pilas.
- Dado un código ya existente, cuando intento registrarlo, entonces el sistema lo rechaza por duplicado.
- Dada una labor con pilas abiertas, cuando intento desactivarla, entonces el sistema lo impide e indica el motivo.

**HU-F01-2: Crear pila (polígono) por labor**
Como Geólogo/Planeamiento, quiero crear una pila asociada a una labor con su código de polígono, para identificar a qué pila pertenece cada carga.
- Dada una labor activa, cuando creo una pila, entonces se genera con un código único y en estado "Abierta".
- Dada una pila abierta, cuando la consulto, entonces veo la labor, la fecha de creación y su número de volquetes (inicialmente 0).
- [supuesto] Una pila pertenece a una sola labor.

### F02: Registro de volquetes

**HU-F02-1: Registrar volquete en una pila**
Como Operador de balanza, quiero registrar cada volquete con placa, tonelaje, fecha/hora y pila, para tener el detalle de lo que ingresa a cada pila.
- Dada una pila "Abierta", cuando registro un volquete con todos sus datos, entonces queda asociado a la pila y se actualiza su tonelaje acumulado.
- Dada una pila "Cerrada", cuando intento asignarle un volquete, entonces el sistema lo rechaza.
- Dado un tonelaje menor o igual a 0 o fuera del rango configurado, cuando guardo, entonces el sistema muestra un error de validación.

**HU-F02-2: Consultar y corregir volquetes del turno**
Como Supervisor de planta, quiero listar los volquetes del turno y corregir datos mal registrados, para asegurar que el tonelaje de la pila sea confiable.
- Dado un turno, cuando filtro por fecha y pila, entonces veo los volquetes con su tonelaje y el estado de su muestra.
- Dado un volquete de una pila abierta, cuando corrijo el tonelaje, entonces se recalcula el acumulado y se registra la auditoría.

### F03: Registro de muestra por volquete

**HU-F03-1: Generar código de muestra del volquete**
Como Muestrero, quiero que el sistema genere el código de muestra al tomar la muestra del volquete, para enviarla al laboratorio sin errores de identificación.
- Dado un volquete sin muestra, cuando registro la toma, entonces se genera un código único vinculado 1:1 al volquete.
- Dado un volquete con muestra, cuando intento registrar otra, entonces el sistema lo impide.
- La muestra queda en estado "Pendiente de ley".

**HU-F03-2: Registrar ley de la muestra**
Como Analista de laboratorio, quiero ingresar la ley de cada muestra por su código, para que se use en el cálculo de la ley de la pila.
- Dado un código de muestra "Pendiente de ley", cuando ingreso la ley [supuesto: Au g/t], entonces la muestra pasa a "Con ley".
- Dado un valor negativo o no numérico, cuando guardo, entonces el sistema lo rechaza.
- Dado un código inexistente, cuando lo busco, entonces el sistema informa que no existe.

### F04: Ley ponderada de pila

**HU-F04-1: Calcular ley ponderada de la pila**
Como Supervisor de planta, quiero ver la ley ponderada de cada pila, para conocer la calidad del mineral disponible.
- Dada una pila con muestras "Con ley", cuando la consulto, entonces la ley = Σ(ley × tonelaje) / Σ tonelaje de esos volquetes.
- Dada una pila con muestras pendientes, cuando la consulto, entonces se muestra la ley parcial y el % de tonelaje con ley.
- [supuesto] Se pondera por tonelaje húmedo (por confirmar con el negocio).

**HU-F04-2: Cerrar pila con ley final**
Como Geólogo/Planeamiento, quiero cerrar una pila cuando todas sus muestras tengan ley, para que quede disponible para el blending con una ley definitiva.
- Dada una pila con 100 % de muestras con ley, cuando la cierro, entonces pasa a "Cerrada" con su ley y tonelaje finales.
- Dada una pila con muestras pendientes, cuando intento cerrarla, entonces el sistema lo impide e indica cuántas faltan.
- Dada una pila cerrada, no se pueden agregar ni modificar volquetes.

### F09: Seguridad y roles

**HU-F09-1: Controlar acceso por ventana**
Como Administrador, quiero asignar permisos por ventana y por acción a cada rol, para que cada usuario solo haga lo que le corresponde.
- Dado un usuario sin permiso sobre una ventana, cuando intenta ingresar, entonces el sistema le niega el acceso.
- Dado un usuario con permiso de solo consulta, cuando intenta grabar, entonces el servidor rechaza la operación.

**HU-F09-2: Auditar cambios en registros críticos**
Como Jefe de planta, quiero consultar quién modificó volquetes, leyes y pilas, para asegurar la trazabilidad ante auditorías.
- Dado un cambio en un volquete, una ley o una pila, cuando se graba, entonces se registran el usuario, la fecha/hora, el valor anterior y el valor nuevo.
- Dado un registro, cuando consulto su historial, entonces veo todos sus cambios en orden cronológico.

### F05: Registro de blending a tolvas

**HU-F05-1: Registrar programa de blending del día**
Como Supervisor de planta, quiero definir cuántas toneladas se enviarán de cada pila a cada tolva, para planificar la carga del día con la ley objetivo.
- Dadas pilas "Cerradas" con stock, cuando asigno toneladas a una tolva, entonces el sistema muestra la ley estimada de la mezcla.
- Dada una asignación mayor al stock de la pila, cuando guardo, entonces el sistema lo rechaza.

**HU-F05-2: Registrar carga real de pila a tolva**
Como Operador de despacho, quiero registrar las toneladas realmente enviadas de cada pila a cada tolva, para descontar el stock y alimentar la carga del día.
- Dado un envío registrado, cuando guardo, entonces se descuenta del stock de la pila origen.
- Dada una pila que llega a stock 0, entonces pasa a estado "Consumida".

### F06: Carga del día

**HU-F06-1: Consolidar carga del día**
Como Supervisor de planta, quiero ver la suma de todas las tolvas del día con su ley de cabeza ponderada, para reportar la alimentación a planta.
- Dada una fecha, cuando consulto, entonces veo el tonelaje por tolva, el tonelaje total y la ley ponderada del día.
- Dado un día cerrado, no se pueden registrar más envíos a tolvas en esa fecha.

**HU-F06-2: Comparar programado vs. real**
Como Jefe de planta, quiero comparar el blending programado con el real, para medir el cumplimiento del programa.
- Dada una fecha, cuando consulto, entonces veo por pila y por tolva las toneladas programadas, las reales y la desviación %.

### F08: Trazabilidad

**HU-F08-1: Rastrear origen de la carga del día**
Como Jefe de planta, quiero navegar desde la carga del día hasta las tolvas, las pilas, los volquetes y las labores, para conocer de dónde vino el mineral procesado.
- Dada una carga diaria, cuando la abro, entonces puedo descender hasta cada volquete y su muestra.

**HU-F08-2: Rastrear destino de una pila**
Como Geólogo/Planeamiento, quiero ver a qué tolvas y días se envió una pila, para conciliar la producción por labor.
- Dada una pila, cuando consulto su destino, entonces veo los envíos por fecha y tolva y el saldo pendiente.

### F07: Integración con laboratorio

**HU-F07-1: Recibir leyes automáticamente**
Como Analista de laboratorio, quiero que las leyes se carguen automáticamente desde el sistema de laboratorio, para evitar la doble digitación.
- Dado un resultado emitido por el laboratorio, cuando se recibe, entonces se actualiza la muestra correspondiente por su código.
- Dado un resultado ya cargado, cuando se recibe de nuevo, entonces no se duplica.

**HU-F07-2: Gestionar errores de integración**
Como Administrador, quiero ver los resultados rechazados por la integración, para corregirlos y reprocesarlos.
- Dado un resultado con un código de muestra inexistente, entonces queda en la bandeja de errores con el motivo.
- Dado un error corregido, cuando lo reproceso, entonces se aplica a la muestra.

### F10: Reportes / tablero

**HU-F10-1: Ver tablero de stock y ley de pilas**
Como Jefe de planta, quiero un tablero con el stock y la ley de las pilas por estado, para decidir el blending de los próximos días.
- Dado el tablero, cuando lo abro, entonces veo las pilas abiertas, cerradas y consumidas con su tonelaje y ley.

**HU-F10-2: Exportar histórico de blending**
Como Supervisor de planta, quiero exportar a Excel el histórico de blending por rango de fechas, para preparar los reportes de gestión.
- Dado un rango de fechas, cuando exporto, entonces se descarga un archivo con el detalle por día, tolva y pila.

### F11: Integración con SIG

**HU-F11-1: Importar polígonos desde el SIG**
Como Geólogo/Planeamiento, quiero importar los polígonos definidos en el SIG, para no registrar las pilas manualmente.
- Dado un archivo o servicio del SIG [supuesto: formato por definir], cuando importo, entonces se crean o actualizan las pilas con su geometría.
- Dado un polígono con una labor inexistente, entonces se reporta como error.

**HU-F11-2: Visualizar pilas en mapa**
Como Geólogo/Planeamiento, quiero ver las pilas sobre un mapa con su estado y ley, para tener una vista espacial del inventario.
- Dado el mapa, cuando selecciono un polígono, entonces veo su código, labor, tonelaje, ley y estado.
