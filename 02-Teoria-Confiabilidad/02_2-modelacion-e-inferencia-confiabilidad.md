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
  pre { border-left: 5px solid #2f6f9f; padding: 0.75em; }
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
### Modelación e inferencia para confiabilidad

### Extensión a teoría de confiabilidad

Patricio Olivares R.<br>
Universidad Técnica Federico Santa María

---

## Propósito y alcance

Con los contenidos anteriores ya podemos definir una misión, elegir una distribución y calcular $R(t)$. El siguiente paso es evaluar qué tan bien se sostiene ese modelo cuando trabajamos con datos reales.

Para ello, abordaremos cuatro preguntas:

1. ¿Cómo se relacionan los conteos de fallas con los tiempos entre fallas?
2. ¿Qué desviación estándar tienen los tiempos hasta la falla y cómo se compara con su promedio?
3. ¿Cómo reconocer una tasa de falla variable y revisar si Weibull es razonable?
4. ¿Cómo cuantificamos la incertidumbre al estimar y demostrar confiabilidad?

<div class="callout">
El objetivo no es agregar una nueva métrica, sino fortalecer la justificación del modelo y de las decisiones que se toman con él.
</div>

---

## Ruta de la presentación

$$
\text{observaciones}
\longrightarrow
\text{proceso aleatorio}
\longrightarrow
\text{distribuci\'on}
\longrightarrow
\text{par\'ametros e incertidumbre}
\longrightarrow
\text{decisi\'on}
$$

| Bloque | Pregunta que responde |
|---|---|
| Poisson y Exponencial | ¿Cuántos eventos ocurren y cuánto se espera hasta el siguiente? |
| Variabilidad y Weibull | ¿El promedio y una tasa constante describen suficientemente los datos? |
| Inferencia y demostración | ¿Qué evidencia respalda una tasa estimada o una meta de confiabilidad? |
| Estadísticos de orden | ¿Cómo se relacionan vidas individuales con una futura arquitectura? |

<div class="bridge">
Las distribuciones adicionales se resumen al final de la presentación en un anexo, sin desarrollarlas en este bloque principal.
</div>

---

## Conteos, tiempos y proceso de Poisson

Como recordatorio, para el número $N(t)$ de eventos observados hasta el tiempo $t$, si los eventos ocurren a tasa constante $\nu$:

$$
N(t)\sim\operatorname{Poisson}(\nu t),
\qquad
P[N(t)=k]=e^{-\nu t}\frac{(\nu t)^k}{k!},
\qquad k=0,1,2,\ldots
$$

Un **proceso de Poisson** con tasa $\nu$ supone, de forma idealizada:

- $N(0)=0$.
- Los conteos en intervalos disjuntos son independientes.
- El comportamiento depende de la duración del intervalo, no de su posición.
- En un intervalo pequeño $\Delta t$, un evento tiene probabilidad aproximada $\nu\Delta t$ y dos o más eventos tienen probabilidad despreciable.

Aquí $\nu t$ es el número medio de eventos en un intervalo de duración $t$.

---

## La conexión entre Poisson y Exponencial

Suponga que las fallas siguen el proceso de Poisson anterior. Sea $W$ el tiempo hasta la siguiente falla.

El evento $W>t$ significa que no ocurrió ninguna falla durante los primeros $t$:

$$
P(W>t)=P[N(t)=0]
$$

Ahora se usa la fórmula de Poisson con $k=0$:

$$
P[N(t)=0]
=e^{-\nu t}\frac{(\nu t)^0}{0!}
=e^{-\nu t}
$$

---

## La conexión entre Poisson y Exponencial

Por tanto, la función de supervivencia, la distribución acumulada y la densidad de $W$ son:

$$
R_W(t)=P(W>t)=e^{-\nu t},
\qquad
F_W(t)=1-e^{-\nu t},
\qquad
f_W(t)=\nu e^{-\nu t}
$$

Así:

$$
W\sim\operatorname{Exp}(\nu)
$$

<div class="bridge">
La Exponencial no aparece como un supuesto separado. Se obtiene al preguntar cuánto tiempo transcurre hasta el primer evento del proceso de Poisson.
</div>

---

## La conexión entre Poisson y Exponencial

Si el conteo y el tiempo de espera describen la misma falla, bajo una tasa constante:

$$
\boxed{\nu=\lambda}
$$

Por eso:

$$
P[N(t)=0]=R_W(t)=e^{-\lambda t}
$$

Si $\nu$ y $\lambda$ describen el mismo evento bajo una tasa constante, son iguales. Si describen procesos diferentes, sus tasas no tienen por qué coincidir.

---

## Una misma tasa, dos preguntas distintas

Una plataforma registra eventos a tasa $\nu=2\ \mathrm h^{-1}$. Si esos eventos son fallas del mismo componente, entonces $\lambda=\nu=2\ \mathrm h^{-1}$.

| Pregunta | Modelo | Resultado |
|---|---|---|
| ¿No ocurre ningún evento durante $30\ \mathrm{min}$? | $N(0.5)\sim\operatorname{Poisson}(1)$ | $P[N(0.5)=0]=e^{-1}\approx0.3679$ |
| ¿El próximo evento tarda más de $30\ \mathrm{min}$? | $W\sim\operatorname{Exp}(2\ \mathrm h^{-1})$ | $P(W>0.5)=e^{-1}\approx0.3679$ |

El resultado coincide porque ambos eventos dicen que no ocurrió ninguna falla en media hora.

<div class="warn">
Esta conexión requiere una tasa aproximadamente constante e independencia de incrementos. No describe por sí sola desgaste ni fallas comunes.
</div>


---

## Promedio, desviación estándar y coeficiente de variación

Para tiempos de vida observados $t_1,\ldots,t_n$:

$$
\bar t=\frac{1}{n}\sum_{i=1}^n t_i,
\qquad
s=\sqrt{\frac{1}{n-1}\sum_{i=1}^n(t_i-\bar t)^2},
\qquad
\widehat C_V=\frac{s}{\bar t}
$$

| Medida | Qué describe | Unidades |
|---|---|---|
| $\bar t$ | duración típica de la muestra | tiempo |
| $s$ | desviación estándar muestral | tiempo |
| $\widehat C_V$ | desviación estándar relativa al promedio | ninguna |

El coeficiente de variación sirve para comparar muestras con escalas distintas. Un valor cercano a $1$ es compatible con la desviación estándar relativa de una Exponencial, aunque no demuestra que el modelo sea correcto.

---

## Ejemplo: el promedio puede ocultar diferencias

Dos grupos de componentes pueden tener el mismo tiempo medio y, sin embargo, comportarse de manera muy distinta:

| Grupo | Tiempos de vida en horas | $\bar t$ | $\widehat C_V$ |
|---|---|---:|---:|
| A | $900,\ 950,\ 1000,\ 1050,\ 1100$ | $1000$ | $0.079$ |
| B | $100,\ 500,\ 1000,\ 1500,\ 1900$ | $1000$ | $0.728$ |

El promedio no distingue estos patrones. El coeficiente de variación sí muestra que el segundo grupo tiene una desviación estándar relativa mucho mayor.

<div class="callout">
El promedio responde cuánto dura una vida típica. El coeficiente de variación responde qué tan grande es la desviación estándar en relación con ese promedio.
</div>

---

## Del supuesto de tasa constante a los datos

La Exponencial no solo fija la tasa $\lambda$. También predice la relación entre el promedio y la desviación estándar:

$$
T\sim\operatorname{Exp}(\lambda),
\qquad
E[T]=\frac{1}{\lambda},
\qquad
\operatorname{SD}(T)=\frac{1}{\lambda},
\qquad
C_V=\frac{\operatorname{SD}(T)}{E[T]}=1
$$

Si los datos se alejan mucho de esta relación, la hipótesis de tasa constante merece una revisión.

---

## Ejemplo: revisar una tasa constante

Los tiempos observados hasta una falla, medidos en horas, son:

$$
200,\ 500,\ 900,\ 1100,\ 1500,\ 1800\ \mathrm h
$$

La muestra entrega:

$$
\bar t=1000\ \mathrm h,
\qquad
s=600\ \mathrm h,
\qquad
\widehat C_V=0.60
$$

La Exponencial predice $C_V=1$. Aquí $\widehat C_V=0.60$, por lo que la desviación estándar relativa es menor que la esperada bajo una tasa constante.

<div class="warn">
Un CV menor que 1 sugiere revisar un modelo Weibull con beta mayor que 1. No es una prueba por sí sola.
</div>

---

## Relación entre $C_V$ y $\beta$ en Weibull

En la familia Weibull, el coeficiente de variación depende de $\beta$, no de la escala $\eta$:

$$
C_V(\beta)=
\sqrt{
\frac{\Gamma(1+2/\beta)}
{\Gamma(1+1/\beta)^2}
-1
}
$$

La relación es:

| Coeficiente de variación | Forma Weibull | Interpretación |
|---|---|---|
| $C_V>1$ | $\beta<1$ | riesgo decreciente y fallas tempranas |
| $C_V=1$ | $\beta=1$ | tasa constante. Caso Exponencial |
| $C_V<1$ | $\beta>1$ | riesgo creciente y posible desgaste |

Por eso, el ejemplo con $\widehat C_V=0.60$ orienta hacia $\beta>1$.

---

## Distribución Weibull: riesgo que cambia con la edad

La Weibull permite que la tasa de falla aumente, disminuya o se mantenga constante:

$$
R(t)=e^{-(t/\eta)^\beta},
\qquad
h(t)=\frac{\beta}{\eta}\left(\frac{t}{\eta}\right)^{\beta-1}
$$

- $\eta>0$ es la escala y tiene unidades de tiempo.
- $\beta>0$ es la forma y controla cómo cambia el riesgo.
- $\beta<1$ indica fallas tempranas.
- $\beta=1$ recupera la Exponencial.
- $\beta>1$ indica riesgo creciente y es compatible con desgaste.

---

## Distribución Weibull: riesgo que cambia con la edad

Ejemplo: el ventilador de un servidor opera continuamente y sus fallas tienden a aparecer por desgaste. Un ajuste inicial propone $\beta=2$ y $\eta=1000\ \mathrm h$:

$$
R(500)=e^{-(500/1000)^2}=e^{-0.25}\approx0.779
$$

La probabilidad de que el ventilador sobreviva una misión de $500\ \mathrm h$ es aproximadamente $77.9\%$. Como $\beta>1$, conviene evaluar reemplazo preventivo antes de que aumente el riesgo.

---

## Elegir un modelo es defender un supuesto

Antes de usar una distribución, revise cuatro aspectos:

1. **Soporte:** ¿la variable puede tomar los valores del modelo?
2. **Mecanismo:** ¿hay desgaste, fallas tempranas o una tasa estable?
3. **Datos:** ¿las observaciones son comparables y contienen censura?
4. **Propósito:** ¿se necesita una explicación simple, una estimación o una predicción operacional?

<div class="warn">
Un modelo puede ajustar numéricamente y, aun así, no ser defendible para la decisión que se quiere tomar.
</div>

---

## De datos a una tasa estimada

Para $d$ fallas observadas durante un tiempo total de exposición $\tau$, bajo un modelo exponencial:

$$
\widehat\lambda=\frac d\tau
$$

Las unidades que aún funcionan al cierre también aportan tiempo a $\tau$. Son observaciones **censuradas**.

$$
\widehat\lambda
\longrightarrow
\widehat{\mathrm{MTTF}}=\frac1{\widehat\lambda}
\longrightarrow
\widehat R(t)=e^{-\widehat\lambda t}
$$

<div class="bridge">
El sombrero indica una estimación desde una muestra. No convierte el parámetro en una certeza.
</div>

---

## Una estimación no es una certeza

La estimación puntual resume los datos, pero su valor depende de cuánta evidencia se haya observado:

$$
\widehat\lambda=\frac d\tau,
\qquad
\widehat R(t)=e^{-\widehat\lambda t}
$$

Dos pruebas pueden entregar la misma tasa estimada:

| | Prueba A | Prueba B |
|---|---:|---:|
| Fallas observadas $d$ | $1$ | $10$ |
| Exposición total $\tau$ | $5000\ \mathrm h$ | $50000\ \mathrm h$ |
| $\widehat\lambda=d/\tau$ | $0.0002\ \mathrm h^{-1}$ | $0.0002\ \mathrm h^{-1}$ |

---

## Una estimación no es una certeza

Para una misión de $300\ \mathrm h$, ambas entregan:

$$
\widehat R(300)=e^{-0.0002(300)}\approx0.942
$$

<div class="callout">
Misma confiabilidad estimada no significa misma confianza.
</div>

---

## ¿Qué significa agregar confianza?

$$
\widehat R(300)=0.942
$$

Este es el mejor valor estimado a partir de la muestra. Pero una decisión de ingeniería suele requerir una afirmación más conservadora:

$$
R(300)\ge0.85
\qquad\text{con confianza }CL=90\%
$$

- $\widehat R(t)$ es una estimación puntual.
- $R_L(t)$ será una cota inferior de confianza.
- La cota inferior es más conservadora que la estimación puntual.

Aquí $CL$ es el nivel de confianza exigido. No describe una probabilidad adicional del producto, sino la evidencia requerida para sostener la afirmación.

La pregunta es qué confiabilidad mínima podemos sostener con los datos disponibles. En confiabilidad, esta cota suele ser más útil que comunicar solo un valor estimado.

---

## De la tasa estimada a una cota conservadora

Bajo el modelo Exponencial, la tasa de falla es aproximadamente constante. Para $d$ fallas durante una exposición total $\tau$:

$$
\widehat\lambda=\frac d\tau
$$

Para construir una afirmación conservadora calculamos una cota superior para la tasa de falla:

$$
\boxed{
\lambda_U=
\frac{\chi^2_{2d+2,\,CL}}{2\tau}
}
$$

---

## De la tasa estimada a una cota conservadora

El término $2d+2$ proviene del conteo Poisson. Para obtener la cota superior se invierte la relación $P(D\le d\mid\lambda_U\tau)=1-CL$, que considera hasta $d$ fallas y no solo exactamente $d$. Esa inversión produce $d+1$ y la parametrización chi cuadrado duplica ese valor:

$$
r=2(d+1)=2d+2
$$

---

## De la cota a una decisión

$\chi^2$ entrega un factor que depende del número de fallas observado y del nivel de confianza. Su valor se obtiene de una tabla, software o función estadística.

Como una tasa mayor implica una confiabilidad menor:

$$
R(t)=e^{-\lambda t}
\qquad\Longrightarrow\qquad
\boxed{R_L(t)=e^{-\lambda_U t}}
$$

Por tanto, podemos afirmar:

$$
\boxed{R(t)\ge R_L(t)\quad\text{con confianza }CL}
$$

$$
\text{datos}
\longrightarrow
\widehat\lambda
\longrightarrow
\lambda_U
\longrightarrow
R_L(t)
\longrightarrow
\text{decisión}
$$

---

## De confiabilidad estimada a confiabilidad respaldada

Durante una prueba se observan $d=2$ fallas en $\tau=10000\ \mathrm h$. Para una misión de $t=300\ \mathrm h$:

$$
\begin{aligned}
\widehat\lambda&=\frac{2}{10000}=0.0002\ \mathrm h^{-1}\\
\widehat R(300)&=e^{-0.0002(300)}\approx0.942
\end{aligned}
$$

Para $CL=90\%$, algunos valores de la tabla son. Si la tabla usa probabilidad de cola superior, se consulta la columna $1-CL=0.10$:

| Grados de libertad $r$ | $q_{r,0.90}$ |
|---:|---:|
| $2$ | $4.605$ |
| $4$ | $7.779$ |
| $6$ | $10.645$ |

---

## De confiabilidad estimada a confiabilidad respaldada

Como $d=2$, usamos $r=2d+2=6$ y, por tanto, $q_{6,0.90}=10.645$:

$$
\begin{aligned}
\lambda_U&=\frac{10.645}{2(10000)}\approx0.000532\ \mathrm h^{-1}\\
R_L(300)&=e^{-0.000532(300)}\approx0.852
\end{aligned}
$$

<div class="callout">
Estimamos una confiabilidad de 94.2%, pero con la evidencia disponible podemos sostener una confiabilidad de al menos 85.2% con 90% de confianza.
</div>

---

## La cota también limita el MTTF

La misma cota sobre $\lambda$ permite obtener una cota conservadora para el MTTF:

$$
\widehat{\mathrm{MTTF}}=\frac1{0.0002}=5000\ \mathrm h
$$

$$
\mathrm{MTTF}_L=\frac1{\lambda_U}
=\frac1{0.000532}\approx1879\ \mathrm h
$$

El MTTF estimado es $5000\ \mathrm h$. Con $90\%$ de confianza podemos sostener un MTTF de al menos aproximadamente $1879\ \mathrm h$.

---

## La misma idea al revés: diseñar una prueba

Hasta ahora preguntamos qué confiabilidad podemos sostener dado un ensayo. Ahora invertimos la pregunta: ¿cuánta evidencia necesitamos para demostrar una confiabilidad mínima?

Si $d$ es el máximo de fallas aceptadas y $\tau$ es el tiempo total de prueba:

$$
R_L(t_D)\ge R_D
$$

Usando la misma cota de confianza:

$$
\boxed{
\tau\ge
\frac{t_D\,\chi^2_{2d+2,\,CL}}
{2[-\ln(R_D)]}
}
$$

<div class="bridge">
La confianza se puede usar en dos direcciones: para evaluar datos disponibles o para planificar la evidencia necesaria.
</div>

---

## Ejemplo: planificar el tiempo de prueba

Para $R_D=0.85$, $t_D=300\ \mathrm h$, $CL=90\%$ y $d=2$:

$$
\tau\ge
\frac{300(10.645)}{2[-\ln(0.85)]}
\approx9825\ \mathrm h
$$

La misma idea se usa en dos direcciones: los datos permiten calcular una cota de confianza y una meta de confiabilidad permite calcular la evidencia necesaria.

<div class="warn">
Estas cotas suponen una tasa de falla aproximadamente constante. Una cota estadística no corrige un modelo mal elegido. Si el riesgo cambia con la edad, corresponde revisar Weibull.
</div>

---

## Validar no es sólo revisar unidades

Una validación mínima del modelo pregunta:

- ¿las condiciones de operación y el evento de falla son comparables?
- ¿la forma observada de supervivencia parece compatible con una tasa constante?
- ¿el riesgo cambia con la edad, sugiriendo Weibull?
- ¿se registraron fallas, tiempos de exposición y censura de forma consistente?
- ¿la evidencia disponible alcanza el nivel de confianza requerido?

Para una Exponencial, $\log R(t)$ debería ser aproximadamente lineal en $t$. Para Weibull, un gráfico de probabilidad o un ajuste por máxima verosimilitud ayuda a revisar $\beta$ y $\eta$.

---

## Estadísticos de orden: varias vidas, una falla del sistema

Sean $T_1,\ldots,T_n$ las vidas de $n$ componentes y ordénelas:

$$
T_{(1)}\le T_{(2)}\le\cdots\le T_{(n)}
$$

| Situación | Tiempo relevante |
|---|---|
| Todos los componentes son necesarios | $T_{(1)}$: primera falla |
| Basta que uno siga funcionando | $T_{(n)}$: última falla |
| Deben funcionar al menos $k$ de $n$ | $T_{(n-k+1)}$: falla número $n-k+1$ |

<div class="bridge">
Esta es la conexión matemática entre vidas individuales y la confiabilidad de una arquitectura. No requiere todavía dibujar un RBD.
</div>

---

## Contexto de ingeniería: de la vida al servicio

La confiabilidad participa durante todo el ciclo de vida del sistema:

| Etapa | Pregunta típica |
|---|---|
| Diseño | ¿qué misión y nivel de servicio se deben exigir? |
| Prueba e introducción | ¿existen fallas tempranas o incertidumbre alta? |
| Operación | ¿qué evidencias actualizan el modelo y qué mantenimiento conviene? |
| Soporte | ¿repuestos, detección y reparación protegen la continuidad? |

La confiabilidad como ingeniería se concentra en requisitos, pruebas y rediseño. La confiabilidad como servicio incorpora logística, repuestos y recuperación.

---

## Ejercicio integrado

Un servicio registra llegadas de alarmas a tasa constante $\nu=4\ \mathrm h^{-1}$. En una prueba independiente de componentes, se observan $d=5$ fallas durante $\tau=2500\ \mathrm h$.

1. Calcule la probabilidad de no recibir alarmas durante $15\ \mathrm{min}$.
2. Calcule la probabilidad de que la próxima alarma tarde más de $15\ \mathrm{min}$.
3. Estime la tasa de falla de un componente bajo modelo exponencial.
4. Indique dos razones por las cuales esta tasa no basta, por sí sola, para decidir un reemplazo preventivo.

Antes de calcular, identifique qué pregunta usa Poisson y cuál usa Exponencial.

---

## Solución e interpretación

Como $15\ \mathrm{min}=0.25\ \mathrm h$:

$$
P[N(0.25)=0]=e^{-(4)(0.25)}=e^{-1}\approx0.3679
$$

$$
P(W>0.25)=e^{-(4)(0.25)}=e^{-1}\approx0.3679
$$

Para la prueba de componentes:

$$
\widehat\lambda=\frac5{2500}=0.002\ \mathrm h^{-1}
$$

La decisión aún requiere, al menos, revisar si la tasa es constante y cuantificar incertidumbre. Un punto estimado no revela desgaste, comparabilidad ni costo de reemplazar.


---

## Cierre

1. Un proceso de Poisson y una Exponencial conectan conteos y tiempos entre eventos bajo supuestos explícitos.
2. Media y variabilidad cumplen funciones distintas. $C_V$ es una señal, no una prueba.
3. Weibull permite revisar si el riesgo cambia con la edad.
4. Una estimación debe comunicar datos, censura, incertidumbre y validación del modelo.
5. Los estadísticos de orden preparan el paso desde componentes individuales a sistemas compuestos.
6. El anexo presenta otras distribuciones y sus supuestos característicos.

---

## Anexo: distribuciones adicionales

La elección de una distribución depende del soporte, el mecanismo, la variabilidad observada y el propósito del análisis. Estas alternativas permiten distinguir entre vidas de componentes, recuperaciones, duraciones acotadas y mediciones.

---

## Anexo: Gamma y Erlang

La Gamma y la Erlang representan un tiempo formado por varias etapas positivas. Si el número de etapas es entero, el modelo se llama Erlang:

$$
T=X_1+\cdots+X_\alpha,
\qquad
X_i\sim\operatorname{Exp}(\lambda)
$$

Sus parámetros son:

- $\alpha$: número de etapas o forma.
- $\lambda$: tasa de cada etapa.

Cuando $\alpha>1$, el riesgo total aumenta a medida que se completan las etapas. Es una estructura propia de recuperaciones y procesos por fases.

La recuperación de un servicio tiene cuatro etapas y el tiempo medio observado es $10\ \mathrm h$:

$$
\alpha=4,
\qquad
\widehat\lambda=\frac{\alpha}{\bar t}=\frac4{10}=0.4\ \mathrm h^{-1}
$$

---

## Anexo: Gamma y Erlang

Entonces:

$$
E[T]=\frac4{0.4}=10\ \mathrm h,
\qquad
C_V=\frac1{\sqrt4}=0.5
$$

Aquí $\alpha=4$ proviene de las etapas y $\lambda$ del tiempo medio. El coeficiente de variación no basta por sí solo para justificar el modelo.

---

## Anexo: Lognormal

La Lognormal representa tiempos positivos y asimétricos afectados por factores multiplicativos, como carga, temperatura y fabricación:

$$
\ln T\sim\operatorname{Normal}(\mu,\sigma^2)
$$

- $\mu$ controla la posición en la escala logarítmica.
- $\sigma$ controla la variabilidad en esa escala.
- La mediana es $e^\mu$.
- La media es $e^{\mu+\sigma^2/2}$.

Con datos completos:

$$
\widehat\mu=\frac1n\sum_i\ln t_i,
\qquad
\widehat\sigma=\operatorname{SD}(\ln t_i)
$$

---

## Anexo: Lognormal

Los efectos de carga y temperatura pueden estimarse mediante una regresión de $\ln T$.

Una prueba de SSD indica una mediana de $10000\ \mathrm h$ y un percentil 90 de $19000\ \mathrm h$:

$$
\widehat\mu=\ln(10000)
$$

Como $q_{0.90}=e^{\mu+1.2816\sigma}$:

$$
\widehat\sigma=
\frac{\ln(19000)-\ln(10000)}{1.2816}
\approx0.494
$$

La vida media estimada es aproximadamente:

$$
\widehat E[T]
=10000e^{0.494^2/2}
\approx11300\ \mathrm h
$$

Los cambios de condiciones desplazan $\mu$, mientras $\sigma$ representa la variabilidad residual.

---

## Anexo: Uniforme

La Uniforme supone que todos los valores dentro de un intervalo son igualmente probables:

$$
T\sim\operatorname{Uniforme}(a,b)
$$

Sus parámetros son los límites:

- $a$: límite inferior.
- $b$: límite superior.

Los límites deben provenir de restricciones físicas o registros confiables, no solo del mínimo y máximo de una muestra pequeña.

Una instalación en terreno tarda entre $8$ y $12$ horas, y no existe evidencia para preferir un tiempo dentro de ese intervalo:

$$
a=8\ \mathrm h,
\qquad
b=12\ \mathrm h
$$

---

## Anexo: Uniforme

Entonces:

$$
E[T]=\frac{8+12}{2}=10\ \mathrm h,
\qquad
P(T\le10)=0.5
$$

La Uniforme sirve para simulaciones preliminares con límites confiables, pero no describe por sí sola un mecanismo de falla.

---

## Anexo: Normal

La Normal representa mediciones o errores aproximadamente simétricos:

$$
X\sim\operatorname{Normal}(\mu,\sigma^2)
$$

Sus parámetros se estiman con:

$$
\widehat\mu=\bar x,
\qquad
\widehat\sigma=\operatorname{SD}(x_i)
$$

---

## Anexo: Normal

Las latencias medidas en un enlace son $95$, $100$ y $105\ \mathrm{ms}$:

$$
\widehat\mu=100\ \mathrm{ms},
\qquad
\widehat\sigma=5\ \mathrm{ms}
$$

Aproximadamente el $68\%$ de las mediciones queda entre $95$ y $105\ \mathrm{ms}$. La Normal resume variaciones alrededor de un valor nominal, pero puede asignar probabilidad a tiempos negativos.

---

## Referencias

- K. S. Trivedi, *Probability and Statistics with Reliability, Queuing, and Computer Science Applications*, 2.ª ed., Wiley, 2002, caps. 2 a 4.
- K. S. Trivedi y A. Bobbio, *Reliability and Availability Engineering: Modeling, Analysis, and Applications*, Cambridge University Press, 2017, caps. 1 a 4.
- S. M. Ross, *Introduction to Probability Models*, 12.ª ed., Academic Press, 2019, caps. 2 a 5.
- NIST/SEMATECH, [tabla de valores críticos de la distribución chi cuadrado](https://www.itl.nist.gov/div898/handbook/eda/section3/eda3674.htm).
- Material histórico TEL211: distribuciones de probabilidad, teoría de confiabilidad y *Expectation*, USM.
