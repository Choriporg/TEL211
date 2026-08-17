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
  img.component-service { display: block; margin: 0 auto; max-height: 390px; width: 90%; }
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

Para ello, abordaremos tres preguntas:

1. ¿Cómo se relacionan los conteos de fallas con los tiempos entre fallas?
2. ¿Qué desviación estándar tienen los tiempos hasta la falla y cómo se compara con su promedio?
3. ¿Cómo reconocer una tasa de falla variable y revisar si Weibull es razonable?

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
\text{par\'ametros y supuestos}
\longrightarrow
\text{decisi\'on}
$$

| Bloque | Pregunta que responde |
|---|---|
| Poisson y Exponencial | ¿Cuántos eventos ocurren y cuánto se espera hasta el siguiente? |
| Variabilidad y Weibull | ¿El promedio y una tasa constante describen suficientemente los datos? |

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

El coeficiente de variación sirve para comparar muestras con escalas distintas. Como regla orientativa, $0.8\leq\widehat C_V\leq1.2$ hace razonable considerar una Exponencial, pero no demuestra que el modelo sea correcto.

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

La probabilidad de que el ventilador sobreviva una misión de $500\ \mathrm h$ es aproximadamente $77.9\%$. Como $\beta>1$, conviene evaluar reemplazo preventivo antes de que aumente el riesgo. Para definir cuándo hacerlo, es útil fijar una confiabilidad mínima aceptable $R_{\min}$. Su valor depende del problema, por ejemplo, de la criticidad de la falla y del costo del reemplazo.

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
El sombrero indica que el valor se estima a partir de una muestra.
</div>

---

## Ejercicio

### 1. Alarmas

Un servicio registra llegadas de alarmas a tasa constante $4\ \mathrm h^{-1}$. Responda las siguientes preguntas indicando qué distribución de probabilidad debe usar en cada caso.

1.1. Calcule la probabilidad de no recibir alarmas durante $15\ \mathrm{min}$.

1.2. Calcule la probabilidad de que la próxima alarma tarde más de $15\ \mathrm{min}$.

### 2. Fallas

En una prueba independiente de componentes, se observan $d=10$ fallas durante $\tau=5000\ \mathrm h$.

Los tiempos de falla observados son:

| Componente | Tiempo de falla (h) | Componente | Tiempo de falla (h) |
|---:|---:|---:|---:|
| 1 | 20 | 6 | 500 |
| 2 | 60 | 7 | 700 |
| 3 | 100 | 8 | 950 |
| 4 | 150 | 9 | 1100 |
| 5 | 250 | 10 | 1170 |

2.1. Estime la tasa de falla de un componente bajo modelo exponencial.

2.2. Calcule $\widehat C_V$ a partir de la tabla. Para una misión de $500\ \mathrm h$, calcule la confiabilidad estimada. Justifique, usando $\widehat C_V$, si corresponde utilizar una distribución Exponencial o Weibull. Si selecciona Weibull, use el ajuste $\beta=2$ y $\eta=1000\ \mathrm h$.

2.3. Se define una confiabilidad mínima aceptable de $75\%$. Bajo el modelo seleccionado, determine el tiempo en que debe programarse el reemplazo preventivo.

---

## Solución

### Respuestas 1.1 y 1.2: alarmas

Como $15\ \mathrm{min}=0.25\ \mathrm h$:

**Respuesta 1.1.** Probabilidad de no recibir alarmas:

$$
P[N(0.25)=0]=e^{-(4)(0.25)}=e^{-1}\approx0.3679
$$

**Respuesta 1.2.** Probabilidad de que la próxima alarma tarde más de $15\ \mathrm{min}$:

$$
P(W>0.25)=e^{-(4)(0.25)}=e^{-1}\approx0.3679
$$

---

## Solución

### Respuestas 2.1, 2.2 y 2.3: fallas

**Respuesta 2.1.** Para la prueba de componentes:

$$
\widehat\lambda=\frac{10}{5000}=0.002\ \mathrm h^{-1}
$$

---

## Solución

### Respuestas 2.1, 2.2 y 2.3: fallas

**Respuesta 2.2.** A partir de la tabla:

$$
\bar t=500\ \mathrm h,
\qquad
s=\sqrt{\frac{1820400}{9}}\approx449.7\ \mathrm h,
\qquad
\widehat C_V=\frac{449.7}{500}\approx0.90
$$

La distribución Exponencial predice $C_V=1$. Como $\widehat C_V\approx0.90$ es cercano a $1$, el supuesto de tasa constante resulta razonable y se utiliza una distribución Exponencial:

$$
\widehat R(500)=e^{-\widehat\lambda(500)}=e^{-(0.002)(500)}=e^{-1}\approx0.3679
$$

El coeficiente de variación orienta la selección del modelo, pero no demuestra por sí solo que la distribución Exponencial sea correcta.

---

## Solución

### Respuestas 2.1, 2.2 y 2.3: fallas


**Respuesta 2.3.** Bajo el modelo Exponencial, se busca el tiempo $t_p$ que satisface $R(t_p)=0.75$:

$$
0.75=e^{-\widehat\lambda t_p}
\qquad\Longrightarrow\qquad
t_p=\frac{-\ln(0.75)}{\widehat\lambda}
=\frac{-\ln(0.75)}{0.002}
\approx143.8\ \mathrm h
$$

El reemplazo preventivo debe programarse aproximadamente a las $144\ \mathrm h$.


---

## Cierre

1. Un proceso de Poisson y una Exponencial conectan conteos y tiempos entre eventos bajo supuestos explícitos.
2. Media y variabilidad cumplen funciones distintas. $C_V$ es una señal, no una prueba.
3. Weibull permite revisar si el riesgo cambia con la edad.
4. Una estimación debe comunicar datos, censura, supuestos y validación del modelo.
5. El anexo presenta otras distribuciones y sus supuestos característicos.

---

## De un componente a un servicio

Hasta ahora se modeló un componente. Para obtener $R_{\mathrm{sistema}}(t)$ también se necesita saber cómo se conectan los componentes.

<img class="component-service" src="images/componente-a-servicio.svg" alt="Comparación entre una arquitectura serie, donde A y B deben funcionar, y una arquitectura en paralelo, donde basta que funcione A o B">

---

## Misma confiabilidad, diseños distintos

Si dos componentes independientes tienen $R_1(t)=R_2(t)=0.9$:

$$
\begin{aligned}
R_{\mathrm{serie}}(t)&=0.9\times0.9=0.81,\\
R_{\mathrm{paralelo}}(t)&=1-(1-0.9)^2=0.99.
\end{aligned}
$$

La misma confiabilidad individual puede producir servicios muy distintos. La siguiente clase generaliza este cálculo a arquitecturas serie, paralelo y $k$-de-$n$.

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
- Material histórico TEL211: distribuciones de probabilidad, teoría de confiabilidad y *Expectation*, USM.
