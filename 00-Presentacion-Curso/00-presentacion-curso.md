---
marp: true
math: mathjax
paginate: true
style: |
  section { font-size: 27px; line-height: 1.35; }
  section.lead { text-align: center; }
  section.lead h1 { font-size: 2.1em; }
  h2, h3 { color: #12394f; }
  code { font-size: 0.84em; }
  table { font-size: 0.77em; }
  .columns { display: grid; grid-template-columns: 1fr 1fr; gap: 32px; align-items: start; }
  .callout { background: #eef6fb; border-left: 6px solid #2f6f9f; border-radius: 6px; padding: 0.7em 0.9em; }
  .bridge { background: #f7f8fa; border-left: 6px solid #6b7280; border-radius: 6px; padding: 0.65em 0.9em; }
  .warn { background: #fff4df; border-left: 6px solid #b7791f; border-radius: 6px; padding: 0.65em 0.9em; }
  .example-space { background: #eef8f1; border-left: 6px solid #2f855a; border-radius: 6px; padding: 0.65em 0.9em; }
  .small { font-size: 0.82em; }
---
<!-- _class: lead -->

# TEL211
## Disponibilidad y Rendimiento de Sistemas TIC
### Presentación y mapa conceptual del curso

Patricio Olivares R.  
Universidad Técnica Federico Santa María

---

## Objetivo de TEL211

Cuando se diseña u opera un servicio de tecnologías de la información y la comunicación (TIC) no basta con preguntar si sus componentes son buenos.

<div class="callout">
¿Cómo justificar, con modelos cuantitativos, que un servicio será suficientemente disponible y responderá con el rendimiento exigido?
</div>

El foco está en transformar fallas, reparaciones, arquitectura y carga en una decisión argumentada.

<div class="columns">
<div>

**Continuidad**

¿El servicio estará operativo cuando se necesite?

</div>
<div>

**Respuesta**

¿El servicio responderá a tiempo bajo la carga esperada?

</div>
</div>

---

## Objetivo de TEL211

El ramo construye un camino de modelado para pasar desde una descripción del sistema hasta una recomendación.

1. Definir qué significa que el servicio funcione.
2. Modelar fallas, reparaciones, estados y carga.
3. Calcular confiabilidad, disponibilidad y métricas de desempeño.
4. Comparar alternativas bajo supuestos explícitos.
5. Recomendar una decisión coherente con el nivel de servicio exigido.

<div class="bridge">
Para ello se aprenderá a elegir la métrica correcta, formular el modelo, calcular con unidades y explicar qué decisión tomar ante los resultados.
</div>

---

## Ejemplo: servicio de pagos

Una interfaz de programación de aplicaciones (API) de pagos depende de dos instancias de aplicación, una base de datos y un enlace de red.

| Pregunta de ingeniería | Herramienta que entregará el curso |
|---|---|
| ¿Sobrevive una misión de 24 horas? | Confiabilidad $R(t)$, probabilidad de no fallar hasta $t$ |
| ¿Tolera la caída de una instancia? | Diagrama de bloques de confiabilidad (RBD) y sistemas $k$-de-$n$, que funcionan con al menos $k$ de $n$ componentes |
| ¿Qué ocurre si los componentes se reparan? | Cadena de Markov en tiempo continuo (CMTC, también CTMC en inglés) y disponibilidad |
| ¿Cuánto demora bajo carga? | Teoría de filas |
| ¿Cumple continuidad y latencia al mismo tiempo? | Modelo integrado |

La misma arquitectura exige modelos distintos según la pregunta.

---

## Ruta de modelado del curso

Esta ruta muestra cómo el curso transforma datos y arquitectura en respuestas sobre continuidad y rendimiento.

$$
\text{datos}
\longrightarrow \text{distribuci\'on}
\longrightarrow \text{componente}
\longrightarrow \text{arquitectura}
\longrightarrow \text{estados}
\longrightarrow \text{servicio bajo carga}
$$

1. **Datos:** observaciones de fallas, reparaciones, tiempos o carga.
2. **Distribución:** modelo probabilístico para tiempos o conteos.
3. **Componente:** confiabilidad de una unidad durante una misión.
4. **Arquitectura:** combinación de componentes mediante diagramas RBD y redundancia.
5. **Estados:** evolución, falla y reparación mediante cadenas de Markov.
6. **Servicio bajo carga:** rendimiento y tiempos de espera mediante filas.

---

## Tres escalas para una misma decisión

En la API de pagos, la respuesta depende del horizonte observado. Cambia la pregunta, cambia el evento y cambia la herramienta.

| Escala | Pregunta | Modelo típico |
|---|---|---|
| Misión | ¿Completa las próximas $24\ \mathrm{h}$ sin fallar? | $R(24)$ |
| Operación | ¿Qué fracción del tiempo está operativa? | Disponibilidad instantánea $A(t)$ y de largo plazo $A_\infty$ |
| Solicitud | ¿Cuánto tarda cada pago bajo carga? | Filas $M/M/1$ y $M/M/c$, notación de Kendall para uno o $c$ servidores |

<div class="warn">
No se debe sustituir una escala por otra. Una alta disponibilidad no garantiza baja latencia, y una baja probabilidad de no fallar no impide operar bien si la reparación es rápida.
</div>

---

## Contenidos del curso por unidades

| Unidad | Tema | Pregunta que prepara |
|---:|---|---|
| 00 | Presentación del curso | ¿Qué problema resuelve TEL211? |
| 01 | Fundamentos de probabilidad | ¿Cómo modelar incertidumbre en tiempos y eventos? |
| 02 | Teoría de confiabilidad | ¿Qué significa sobrevivir una misión? |
| 03 | Diagramas de confiabilidad | ¿Cómo afecta la arquitectura del sistema? |
| 04 | Procesos estocásticos y cadenas de Markov en tiempo discreto (DTMC) | ¿Cómo evoluciona un sistema por estados discretos? |
| 05 | CMTC o CTMC y disponibilidad | ¿Cómo modelar fallas y reparaciones en tiempo continuo? |
| 06 | Métricas de disponibilidad | ¿Qué métrica responde a cada decisión operacional? |
| 07 | Teoría de filas | ¿Cómo relacionar carga, capacidad y espera? |

---

## Calificación final

Según el programa vigente:

$\overline C$ es el promedio de certámenes, $\overline T$ el promedio de tareas y $NF$ la nota final.

$$
\overline C=\frac{C_1+C_2+C_3}{3},
\qquad
\overline T=\frac{T_1+T_2+T_3}{3}
$$

Si $\overline C\ge 45$:

$$
NF=0.6\,\overline C+0.4\,\overline T
$$

Si $\overline C<45$:

$$
NF=\overline C
$$

La nota mínima de aprobación es $55$ en escala de $0$ a $100$.

---

## Certámenes y tareas

Los certámenes evalúan la capacidad de resolver problemas en tiempo acotado.

- Identificar la métrica adecuada.
- Formular el modelo.
- Calcular con unidades consistentes.
- Interpretar el resultado.

Las tareas permiten desarrollar problemas con más contexto.

- Pueden incluir análisis escrito, desarrollo matemático y apoyo computacional.
- Deben mostrar el razonamiento, no solo el resultado final.
- El código puede verificar un cálculo, pero no reemplaza la formulación ni la interpretación.

---

## Estructura mínima de una tarea

1. **Problema y frontera:** qué sistema se analiza y qué queda fuera.
2. **Variables y unidades:** significado de cada símbolo.
3. **Supuestos:** independencia, distribuciones y régimen temporal.
4. **Modelo y desarrollo:** diagrama y ecuaciones antes de sustituir valores.
5. **Resultado y validación:** cotas, unidades o cálculo alternativo.
6. **Conclusión y fuentes:** decisión respaldada y material utilizado.

<div class="callout">
Una buena entrega permite revisar qué se calculó, por qué ese modelo corresponde y qué decisión se desprende del resultado.
</div>

---

## Cómo afrontar un problema

Antes de calcular, conviene ordenar la respuesta:

1. **Evento o métrica:** qué se desea conocer.
2. **Frontera:** qué componentes pertenecen al sistema.
3. **Variables:** qué representa cada símbolo y en qué unidades.
4. **Supuestos:** independencia, distribución, reparación, régimen temporal.
5. **Modelo:** diagrama, cadena, ecuación o fila.
6. **Cálculo:** sustitución numérica y resultado.
7. **Validación:** unidades, cotas o caso límite.
8. **Interpretación:** decisión que permite tomar.

---

## Ejemplo de problema del curso

Un servidor falla con tasa $\lambda=2\times10^{-4}\ \mathrm{h}^{-1}$ y se repara con tasa $\mu=0.1\ \mathrm{h}^{-1}$.

Se quieren responder dos preguntas distintas:

1. ¿Cuál es la probabilidad de que complete una misión de $1000\ \mathrm{h}$ sin fallar?
2. ¿Qué fracción del tiempo estará operativo a largo plazo si puede repararse?

<div class="bridge">
El punto no es aprender hoy todas las fórmulas. El punto es ver que cada pregunta define una métrica distinta.
</div>

---

## Lectura del ejemplo

**Confiabilidad de la misión:** probabilidad de completar el intervalo sin fallar.

$$
R(1000)=e^{-\lambda 1000}=e^{-0.2}\approx 0.8187
$$

**Disponibilidad estacionaria:** fracción de largo plazo en operación, permitiendo falla y reparación.

$$
A_\infty=\frac{\mu}{\lambda+\mu}
=\frac{0.1}{0.1002}\approx 0.9980
$$

<div class="callout">
Ambos resultados pueden ser correctos. Uno exige no fallar durante la misión. El otro permite fallar, reparar y volver a operar.
</div>

---

## A considerar

- El objetivo no es memorizar fórmulas, sino elegir el modelo correcto.
- Confiabilidad, disponibilidad y rendimiento responden preguntas diferentes.
- Toda respuesta debe declarar evento, horizonte, unidades y supuestos.
- La misma arquitectura puede requerir modelos distintos según la escala temporal.
- El curso avanza desde componentes hasta servicios bajo carga.

<div class="bridge">
La siguiente presentación comienza el modelado con un repaso selectivo de probabilidad.
</div>

---

## Textos guía para profundizar

- Trivedi y Bobbio, *Reliability and Availability Engineering*. Útil para confiabilidad, disponibilidad y modelos reparables.
- Shortle, Thompson, Gross y Harris, *Fundamentals of Queueing Theory*. Útil para filas, carga y rendimiento.
- Ross, *Introduction to Probability Models*. Útil para probabilidad, procesos estocásticos y cadenas de Markov.

Estos textos son apoyo para profundizar. El desarrollo del curso seguirá el material publicado para cada unidad.
