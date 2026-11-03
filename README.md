# M10 · Predeccion de abando estudiantial

> **Hackatón IIA · UADE · 3.4.257.** Este README es la plantilla del grupo. En su fork, reemplacen todo lo que está entre `< >` y borren esta nota. Las bases están en `BASES.md` y su ficha en `problematicas/`.

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

**Línea base trivial.** <Qué resultado saca un sistema que responde siempre lo mismo, y cómo se compara el suyo.>

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | <> |
| ¿Qué métrica? | <> |
| ¿Comparado contra qué? | <> |
| ¿Quién lo midió? | <> |

## 6. Riesgo: el camino completo

<Datos → modelo → decisión → consecuencia aplicado a su caso. Al menos un riesgo concreto.>

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | <> | <> | <> |

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | <> | <> | <> |
