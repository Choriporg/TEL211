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
Universidad Técnica Federico Santa María (USM)

<div class="small">TIC: Tecnologías de la Información y la Comunicación.</div>

---

## La pregunta central

Una plataforma puede tener componentes muy confiables y aun así entregar un mal servicio.

<div class="columns">
<div>

**Disponibilidad**

¿El servicio está operativo cuando se necesita?

$$
A=P(\text{servicio operativo en un instante})
$$

</div>
<div>

**Rendimiento**

¿Con qué latencia y capacidad responde bajo carga?

$$
W=\frac{L}{\lambda_{\mathrm e}}
$$

</div>
</div>

<div class="callout">
El ramo enseña a convertir una arquitectura y su carga en un modelo cuantitativo que permita justificar decisiones de diseño.
</div>

---

## Resultado de aprendizaje en lenguaje de ingeniería

Al terminar el curso se espera poder:

1. **Definir** qué significa que un servicio funcione.
2. **Modelar** fallas, reparaciones, estados y carga.
3. **Calcular** confiabilidad, disponibilidad y métricas de desempeño.
4. **Comparar** alternativas bajo supuestos explícitos.
5. **Recomendar** una decisión coherente con el nivel de servicio exigido.

<div class="bridge">
Una cifra sin evento, horizonte temporal, unidad y supuesto de independencia no constituye una respuesta de ingeniería.
</div>

---

## Caso conductor: interfaz de programación de aplicaciones (API) de pagos

La API depende de dos instancias de aplicación, una base de datos y un enlace de red.

| Pregunta | Herramienta del curso |
|---|---|
| ¿Sobrevive una misión de 24 horas? | Confiabilidad $R(t)$ |
| ¿Tolera la caída de una instancia? | Diagrama de bloques de confiabilidad (RBD) y sistemas $k$-de-$n$ |
| ¿Qué ocurre al reparar componentes? | Cadena de Markov en tiempo continuo (CTMC) y disponibilidad |
| ¿Cuánto demora bajo carga? | Teoría de filas |
| ¿Cumple simultáneamente continuidad y latencia? | Modelo integrado |

Cada unidad vuelve a este caso con una pregunta más exigente.

---

## La secuencia completa

$$
\text{datos}
\longrightarrow \text{distribución}
\longrightarrow \text{componente}
\longrightarrow \text{arquitectura}
\longrightarrow \text{estados}
\longrightarrow \text{servicio bajo carga}
$$

1. Probabilidad y tiempos de vida.
2. Confiabilidad y diagramas RBD.
3. Cadenas de Markov en tiempo discreto (DTMC) y continuo (CTMC).
4. Disponibilidad y mantenibilidad.
5. Filas y rendimiento.
6. Integración y decisión.

---

## Tres escalas temporales distintas

| Escala | Ejemplo | Modelo típico |
|---|---|---|
| Misión | No fallar durante las próximas $24\ \mathrm{h}$ | $R(24)$ |
| Operación | Alternar entre falla y reparación durante meses | CTMC, $A(t)$, $A_\infty$ |
| Solicitud | Atender un paquete en menos de $50\ \mathrm{ms}$ | $M/M/1$, $M/M/c$ |

<div class="warn">
No se debe sustituir confiabilidad por disponibilidad: un sistema reparable puede tener baja probabilidad de no fallar y, sin embargo, alta fracción de tiempo operativo.
</div>

---

## Unidades del programa vigente

El programa `112047401.pdf` organiza los contenidos obligatorios en:

- Teoría de confiabilidad.
- Introducción a procesos estocásticos y cadenas de Markov.
- Análisis de desempeño y disponibilidad de sistemas telemáticos.
- Elementos de teoría de filas.

<div class="small">
La solicitud automática de repetición (ARQ, *Automatic Repeat reQuest*), teoría de información, turbocódigos y validación formal aparecen en versiones antiguas del ramo. Se conservan al final del material como complementos, no como núcleo obligatorio de 2026.
</div>

---

## Cómo se justificará una solución

Para cada problema se seguirá el mismo guion:

1. **Evento y métrica:** qué se desea calcular.
2. **Estado o variable:** qué representa cada símbolo.
3. **Supuestos:** independencia, estacionariedad, distribución, capacidad.
4. **Modelo:** diagrama y ecuaciones.
5. **Cálculo:** unidades y resultado.
6. **Validación:** cotas, caso límite o cálculo alternativo.
7. **Interpretación:** decisión que permite tomar.

---

## Estructura mínima de una tarea

1. **Problema y frontera:** qué sistema se analiza y qué queda fuera.
2. **Variables y unidades:** significado de cada símbolo.
3. **Supuestos:** independencia, distribuciones y régimen temporal.
4. **Modelo y desarrollo:** diagrama y ecuaciones antes de sustituir valores.
5. **Resultado y validación:** cotas, unidades o cálculo alternativo.
6. **Conclusión y referencias:** decisión respaldada y fuentes utilizadas.

<div class="callout">
El código puede verificar el cálculo, pero no reemplaza la formulación ni la interpretación.
</div>

---

## Comprobaciones que acompañan cada entrega

- Si es probabilidad: $0\le p\le1$.
- Si es una matriz de transición: cada fila de $P$ suma $1$.
- Si es una generadora: cada fila de $Q$ suma $0$.
- Si es una fila: comprobar estabilidad antes de usar el estacionario.
- En todo cálculo: revisar unidades y un caso límite.

Los ejemplos Python quedan junto a la materia que verifican y se ejecutan con Python 3 sin dependencias externas.

---

## Ejemplo diagnóstico

Un componente falla con tasa $\lambda=2\times10^{-4}\ \mathrm{h}^{-1}$ y se repara con tasa $\mu=0.1\ \mathrm{h}^{-1}$.

**Confiabilidad de una misión de $1000\ \mathrm{h}$:**

$$
R(1000)=e^{-\lambda 1000}=e^{-0.2}\approx 0.8187
$$

**Disponibilidad estacionaria:**

$$
A_\infty=\frac{\mu}{\lambda+\mu}
=\frac{0.1}{0.1002}\approx 0.9980
$$

<div class="callout">
Ambos resultados son correctos: responden preguntas distintas.
</div>

---

## Evaluación oficial

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

## Qué debe quedar claro hoy

- El objetivo no es memorizar fórmulas, sino **elegir el modelo correcto**.
- Confiabilidad, disponibilidad y rendimiento responden preguntas diferentes.
- Toda respuesta debe declarar evento, horizonte, unidades y supuestos.
- La progresión del curso va desde el componente hasta el servicio bajo carga.

<div class="bridge">
La siguiente presentación comienza el modelado con un repaso selectivo de probabilidad.
</div>

---

## Referencias

- Universidad Técnica Federico Santa María, *Programa de Asignatura TEL211: Disponibilidad y Rendimiento de Sistemas TIC*, aprobado el 6 de enero de 2026.
- K. S. Trivedi y A. Bobbio, *Reliability and Availability Engineering: Modeling, Analysis, and Applications*, Cambridge University Press, 2017, caps. 1–3.
- J. F. Shortle, J. M. Thompson, D. Gross y C. M. Harris, *Fundamentals of Queueing Theory*, 5.ª ed., Wiley, 2018, cap. 1.
