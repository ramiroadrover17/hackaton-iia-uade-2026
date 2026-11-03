# M10 · ¿Este estudiante abandona primer año?


**Integrantes:** Ramiro Adrover - Gonzalez Gori Bruno
**IA declarada:** Claude
**Pista:** Reglas
**Publicación en LinkedIn:** <link>

---

## 1. Formulación y PEAS

**La pregunta.** Para cada caso, ¿este estudiante abandona antes de
terminar primer año? SÍ significa: el estudiante deja la carrera antes
de completar el primer año. Cada caso es un ingresante evaluado al
cierre del primer cuatrimestre

| PEAS | Su sistema |
|---|---|
| Medida de rendimiento | Cuántos de los ingresantes que efectivamente abandonan fueron detectados a tiempo, sin inflar tanto las alertas que el programa de tutorías quede sin cupos útiles.|
| Entorno | La secretaría académica de una universidad, al cierre del primer cuatrimestre, con el legajo administrativo y académico de cada ingresante. |
| Actuadores | Marcar al estudiante en el listado de alerta temprana y asignarle un cupo de tutoría de acompañamiento. |
| Sensores | Tres datos del legajo al cierre del cuatrimestre: asistencia (% del cuatrimestre), materias aprobadas (de 5) y horas de trabajo por semana. |

**Objetivo y métrica.** No son lo mismo, y confundirlos es el error
central de este problema. El objetivo es retener estudiantes: que
ningún ingresante en riesgo se quede sin tutoría. La metrica es la
sensibilidad (de los que abandonan, cuántos detectamos), porque el
falso negativo cuesta un año de vida de una persona y el falso positivo
cuesta un cupo de tutoría. 

Elegimos esa métrica por el costo asimétrico de los errores. Los dos
errores no valen lo mismo:

- **Falso positivo:** le ofrecemos tutoría a alguien que iba a seguir.
  Cuesta un cupo de tutoría, y es recuperable.
- **Falso negativo:** no detectamos a quien abandona. La universidad
  pierde al estudiante y él pierde el año. No es recuperable.

Como el falso negativo es el error caro, el sistema está calibrado para
equivocarse del lado barato: preferimos alertar de más antes que dejar
pasar a un estudiante en riesgo. Eso se mide con sensibilidad, no con
exactitud: un sistema puede tener exactitud alta y sensibilidad cero.

La métrica sigue siendo un proxy imperfecto del objetivo: la
sensibilidad sola se maximiza diciendo SÍ a todos, lo cual no retiene a
nadie y vacía el programa de tutorías. Por eso la reportamos junto a
precisión y especificidad, y la comparamos contra la línea base trivial
(sección 5).


## 2. El sistema

**Cómo decide.** <Neurona: pesos y sesgo. Reglas: la lista de reglas si‑entonces. Agente: la regla con la que elige la acción.>

**Variables que usa y por qué.** <Cuáles usa, cuáles descarta y por qué.>

**Cómo se corre en vivo.** <Pasos concretos para resolver un caso nuevo: link al Colab, a la planilla o la cuenta a mano.>

**Dos casos de ejemplo.**

| Caso inventado | Valores | Salida del sistema |
|---|---|---|
| A | <…> | <SÍ/NO> |
| B | <…> | <SÍ/NO> |

## 3. Medición

**Entrenamiento (casos 1–14)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 7 | FN = 0 |
| **Real NO** | FP = 1 | VN = 6 |

**Prueba (casos 15–20)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 2 | FN = 0 |
| **Real NO** | FP = 0 | VN = 4 |

| Métrica | Entrenamiento | Prueba |
|---|---|---|
| Exactitud | 13/14 = 92.9% | 6/6 = 100% |
| Precisión | 7/8 = 87.5% | 2/2 = 100% |
| Sensibilidad | 7/7 = 100%  | 2/2 = 100% |
| Especificidad | 6/7 = 85.7% | 4/4 = 100% |

**Entrenamiento contra prueba.** La prueba da mejor que el
entrenamiento, que es lo contrario de lo que se esperaría si hubiera
sobreajuste: un sistema sobreajustado memoriza el entrenamiento y se
cae en la prueba. Acá no pasa, y hay dos razones.

Primero, el sistema tiene solo dos umbrales y ninguno se eligió mirando
los casos 15 a 20, así que no hay nada memorizado para que se caiga.

Segundo, y más importante: **el 100% de la prueba no significa que el
sistema sea perfecto**. Son 6 casos, así que cada caso vale 16,7 puntos
porcentuales; con un solo caso distinto la exactitud bajaría a 83,3%.
La conclusión honesta es que el sistema generaliza — el comportamiento
en prueba es consistente con el de entrenamiento — pero la muestra de
prueba es demasiado chica para afirmar más que eso.

Lo que sí se sostiene en los dos conjuntos es la sensibilidad del 100%:
ningún estudiante que abandonó quedó sin detectar, ni entre los 14 ni
entre los 6. Es exactamente lo que el objetivo pide.

## 4. El error más caro

<¿Es peor el falso positivo o el falso negativo en su problema? ¿Por qué, según la ficha? ¿Qué cambiaron en el sistema para cometer menos ese error?>

## 5. ¿Comparado contra qué?

**Línea base trivial.** Un sistema que responde siempre **NO** (nadie
abandona, no se ofrece ninguna tutoría)

| | Línea base "siempre NO" | Nuestro sistema |
|---|---|---|
| Exactitud en entrenamiento | 7/14 = 50% | 13/14 = 92,9% |
| Exactitud en prueba | 4/6 = 66,7% | 6/6 = 100% |
| Sensibilidad en entrenamiento | 0/7 = 0% | 7/7 = 100% |
| Sensibilidad en prueba | 0/2 = 0% | 2/2 = 100% |

Lo importante no es la diferencia de exactitud, sino esto: **la línea
base saca 66,7% de exactitud en prueba y detecta cero estudiantes**. De
los 9 casos que abandonan en toda la ficha, no encuentra ninguno. Es el
ejemplo exacto de por qué la exactitud sola engaña: un número aceptable
sobre un sistema que no sirve para nada, porque nunca ofrece una
tutoría.

El límite opuesto, responder siempre **SÍ**, logra la misma
sensibilidad que nosotros (100%) pero con especificidad 0%: manda a los
20 estudiantes a tutoría. Nuestro sistema alcanza esa sensibilidad
derivando solo 8 de 14 en entrenamiento y 2 de 6 en prueba. Ese es el
valor agregado real: la misma detección con una fracción de los cupos.

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | Los 20 casos de la ficha M10, inventados por la cátedra. 14 de entrenamiento y 6 de prueba, división fija. Cuatro variables disponibles, de las que el sistema usa tres. |
| ¿Qué métrica? | Sensibilidad como métrica principal, por el costo asimétrico de los errores. Se reporta junto a exactitud, precisión y especificidad, nunca la exactitud sola. |
| ¿Comparado contra qué? | Contra la línea base trivial "siempre NO" (50% y 66,7% de exactitud, 0% de sensibilidad) y contra el extremo opuesto "siempre SÍ". |
| ¿Quién lo midió? | Nosotros mismos, sobre los datos provistos por la cátedra. No es una medición independiente, y los casos son inventados: los resultados no se pueden extrapolar a una universidad real. |

## 6. Riesgo: el camino completo

<Datos → modelo → decisión → consecuencia aplicado a su caso. Al menos un riesgo concreto.>

## 7. Bitácora

**Uso de la IA**

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | Que leyera las bases y nuestra ficha, y nos dijera qué pedía cada criterio de la rúbrica. | El mapeo de las siete secciones del README contra los criterios A a G, y la lista de topes. | Nada. |
| 2 | Que propusiera un sistema usando las cuatro variables de la ficha. | El hallazgo de que "pidió constancia de baja" clasifica los 20 casos sin un solo error. | Usar esa variable. Acierta todo porque es la variable objetivo disfrazada de predictor: cuando el dato existe, el estudiante ya se fue. Un sistema con exactitud perfecta y cero capacidad de prevenir. |
| 3 | Que eligiera entre neurona, reglas y agente. | Reglas: con dos condiciones se resuelve un caso nuevo en la mesa a mano y se explica sin ayuda. | La neurona. Con 14 casos de entrenamiento y tres variables no aporta nada que las reglas no capturen, y obliga a hacer la suma ponderada delante del profe. |
| 4 | Que fijara los umbrales de las reglas. | La estructura de dos condiciones: asistencia baja **y** pocas materias aprobadas. | El primer umbral que propuso (asistencia ≤ 72%), que clasificaba los 20 casos sin error. Ese 72 estaba calibrado sobre el caso 19, que es de prueba. Lo cambiamos por 75%, que es el criterio de asistencia mínima habitual y se fija mirando solo el entrenamiento. |
| 5 | Que redactara el objetivo y la métrica. | La distinción entre objetivo (retener estudiantes) y métrica (sensibilidad), justificada por el costo asimétrico de los errores. | La primera versión, que dejaba la sensibilidad como única métrica. Se maximiza diciendo SÍ a todos, lo cual vacía el programa de tutorías. Agregamos precisión y especificidad al reporte. |
| 6 | Que calculara las matrices de confusión. | Los conteos y las cuatro métricas de ambos conjuntos. Verificamos a mano que cierren: 7+0+1+6 = 14 y 2+0+0+4 = 6. | Presentar el 100% de la prueba como un logro. Son 6 casos: cada uno vale 16,7 puntos porcentuales. Lo escribimos como limitación de la muestra, no como mérito del sistema. |
| 7 | Que revisara el falso positivo del caso 12 y propusiera cómo corregirlo. | Documentarlo de forma explícita: lo nombramos en la sección 3 con sus valores y lo usamos en la sección 4 como ejemplo del error barato. | Corregirlo. Ajustar los umbrales para que el caso 12 dé NO nos haría perder casos que sí abandonan, y la sensibilidad es justo lo que el objetivo pide. Es un falso positivo elegido, no un defecto. |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---------|------|----------------------|---------------|
| 1 | <> | <> | <> |
