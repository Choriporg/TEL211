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
  img.diagram { display: block; margin: 0 auto; max-height: 430px; max-width: 100%; }
---
<!-- _class: lead -->

# TEL211
## Disponibilidad y Rendimiento de Sistemas TIC
### Teoría de confiabilidad: del tiempo de vida a una decisión

Patricio Olivares R.  
Universidad Técnica Federico Santa María

---

## Propósito y punto de partida

La clase anterior entregó distribuciones para conteos y tiempos. Ahora se usará una variable:

$$
T=\text{tiempo hasta que el componente deja de cumplir su funci\'on}
$$

Al finalizar podrá:

- definir el evento de falla y la misión
- relacionar $F(t)$, $f(t)$, $R(t)$ y $h(t)$
- calcular confiabilidad y vida media
- elegir entre modelos exponencial y Weibull
- interpretar el resultado sin confundirlo con disponibilidad.

---

## Antes de calcular: ¿qué significa "falla"?

Para el desarrollo de una API crítica, una falla podría ser:

- no responder
- superar $200\ \mathrm{ms}$ de latencia
- responder con datos incorrectos
- perder más de $10^{-4}$ de las solicitudes.

Una afirmación de confiabilidad debe declarar:

$$
\boxed{\text{funci\'on}+\text{condiciones}+\text{horizonte temporal}}
$$

<div class="warn">
"El servidor es 99.9% confiable" es una declaración incompleta si no se define el evento y el tiempo de misión.
</div>

---

## Cuatro funciones, una misma variable

Sea $T\ge 0$ una variable aleatoria continua, que representa el tiempo hasta la falla.

| Pregunta | Función | Qué entrega |
|---|---|---|
| ¿Qué probabilidad hay de fallar antes de $t$? | $F(t)=P(T\le t)$ | falla acumulada |
| ¿Qué probabilidad hay de completar una misión de duración $t$? | $R(t)=P(T>t)$ | confiabilidad |
| ¿En qué tiempos se concentran las fallas? | $f(t)=\dfrac{dF(t)}{dt}$ | forma de la distribución |
| ¿Cómo cambia el riesgo para quienes ya sobrevivieron hasta $t$? | $h(t)=\dfrac{f(t)}{R(t)}$ | tasa condicional de falla |

Estas funciones son formas de responder preguntas distintas sobre el mismo tiempo de vida.

<div class="bridge">
Para una misión se suele reportar R(t). Las otras funciones ayudan a obtenerla, interpretarla y decidir si el modelo usado tiene sentido.
</div>

---

## Falla acumulada y confiabilidad

$F(t)$ obtiene la probabilidad de que la falla ya haya ocurrido hasta el tiempo $t$.

$R(t)$ obtiene el evento complementario: sobrevivir más allá de $t$.

$$
R(t)=1-F(t)
$$

Para una misión de duración $t$, $R(t)$ es la probabilidad de completar la misión sin fallar.

<div class="callout">
La misma variable T produce dos lecturas complementarias: falla acumulada y supervivencia.
</div>

---

## Densidad: dónde se ubican las fallas

La densidad muestra cómo se reparte la probabilidad de falla sobre el tiempo.

Si $F(t)$ acumula probabilidad de falla, $f(t)$ mide cómo crece esa acumulación alrededor de $t$.

$$
f(t)=\frac{dF(t)}{dt}=-\frac{dR(t)}{dt}
$$

La probabilidad en un intervalo se obtiene como área bajo la densidad.

---

## Confiabilidad como área restante

La confiabilidad también se puede leer desde la densidad:

$$
R(t)=\int_t^\infty f(u)\,du
$$

El área a la derecha de $t$ reúne todos los tiempos de falla mayores que $t$.

<div class="bridge">
Por eso R(t) responde si el componente sobrevive más allá del tiempo de misión.
</div>

---

## Riesgo condicional: evento y condición

$R(t)$ entrega la supervivencia desde el inicio.

Para medir el riesgo después de $t$, se condiciona a que el componente llegó funcionando hasta ese tiempo.

| Elemento | Significado |
|---|---|
| $T>t$ | el componente sobrevivió hasta $t$ |
| $t<T\le t+\Delta t$ | falla dentro del intervalo siguiente |

---

## Riesgo condicional: evento y condición

El evento de falla en el intervalo se obtiene como caída de supervivencia:

$$
P(t<T\le t+\Delta t)=R(t)-R(t+\Delta t)
$$

Por probabilidad condicional:

$$
P(A\mid B)=\frac{P(A\cap B)}{P(B)}
$$

con $A=\{t<T\le t+\Delta t\}$ y $B=\{T>t\}$:

$$
P(t<T\le t+\Delta t\mid T>t)
=\frac{R(t)-R(t+\Delta t)}{R(t)}
$$

---

## Riesgo condicional en un intervalo pequeño

La probabilidad anterior depende del tamaño del intervalo $\Delta t$.

Para convertirla en una intensidad local, se divide por la duración del intervalo:

$$
\frac{P(t<T\le t+\Delta t\mid T>t)}{\Delta t}
=\frac{R(t)-R(t+\Delta t)}{R(t)\Delta t}
$$

Al hacer el intervalo cada vez más pequeño:

$$
\lim_{\Delta t\to 0}
\frac{R(t)-R(t+\Delta t)}{\Delta t}
=-R'(t)=f(t)
$$

Por eso:

$$
h(t)=\frac{f(t)}{R(t)}
$$

---

## Del riesgo a la confiabilidad

**Idea central:** si conocemos cómo evoluciona $h(t)$, podemos reconstruir $R(t)$.

Partimos de:

$$
h(t)=\frac{f(t)}{R(t)}
$$

Como $f(t)=-R'(t)$:

$$
h(t)=-\frac{R'(t)}{R(t)}
=-\frac{d}{dt}\ln R(t)
$$

El logaritmo aparece porque:

$$
\frac{d}{dt}\ln R(t)=\frac{R'(t)}{R(t)}
$$

---

## Del riesgo a la confiabilidad

Integrando desde $0$ hasta $t$:

$$
\int_0^t h(u)\,du
=-\left[\ln R(t)-\ln R(0)\right]
$$

Como al inicio el componente funciona, $R(0)=1$ y $\ln R(0)=0$. Definimos:

$$
H(t)=\int_0^t h(u)\,du
\qquad\Longrightarrow\qquad
\boxed{R(t)=e^{-H(t)}}
$$

---

## Del riesgo a la confiabilidad

$$
\boxed{
h(t)
\quad\xrightarrow{\text{integrar}}\quad
H(t)
\quad\xrightarrow{\,e^{-H(t)}\,}\quad
R(t)
}
$$

<div class="callout">
h(t) representa el riesgo de falla en cada instante.<br>
H(t) es el riesgo acumulado desde el inicio.<br>
R(t) indica la probabilidad de seguir funcionando después de acumular ese riesgo.
</div>

---

## Caso de riesgo constante

La hipótesis más simple es que el riesgo no cambia con el tiempo:

$$
h(t)=\lambda
$$

$$
H(t)=\int_0^t\lambda\,du=\lambda t
$$

$$
\boxed{
h(t)=\lambda
\quad\xrightarrow{\text{integrar}}\quad
H(t)=\lambda t
\quad\xrightarrow{\,e^{-H(t)}\,}\quad
R(t)=e^{-\lambda t}
}
$$

<div class="callout">
La distribución <strong>Exponencial</strong> aparece cuando suponemos que la tasa de falla es constante.
</div>

Si $h(t)$ cambia con el tiempo, la misma relación permite obtener otros modelos, como Weibull.

---

## Ejemplo guiado: misión de un router

Un router tiene tasa de falla constante
$\lambda=2\times10^{-5}\ \mathrm h^{-1}$.

Para una misión de un año, $t=8760\ \mathrm h$:

$$
R(8760)=e^{-(2\times10^{-5})(8760)}
=e^{-0.1752}\approx 0.8393
$$

Por tanto:

$$
P(T\le8760)=1-R(8760)\approx0.1607
$$

El resultado se refiere a un router **sin reparación durante la misión**.

---

## La exponencial y la falta de memoria

Si $T$ es exponencial:

$$
P(T>s+t\mid T>s)=P(T>t)=e^{-\lambda t}
$$

Ejemplo: si el router anterior ya sobrevivió $5000\ \mathrm h$, la probabilidad de sobrevivir otras $1000\ \mathrm h$ es:

$$
P(T>6000\mid T>5000)=e^{-0.02}\approx0.9802
$$

<div class="warn">
La igualdad proviene del modelo, no de que los equipos reales "rejuvenezcan". Debe justificarse con el mecanismo y los datos.
</div>

---

## Modelo Weibull: riesgo variable

Con forma $\beta>0$ y escala $\eta>0$:

$$
R(t)=e^{-(t/\eta)^\beta}
$$

$$
f(t)=\frac{\beta}{\eta}\left(\frac{t}{\eta}\right)^{\beta-1}
e^{-(t/\eta)^\beta}
$$

$$
h(t)=\frac{\beta}{\eta}\left(\frac{t}{\eta}\right)^{\beta-1}
$$

Cuando $\beta=1$, Weibull se reduce a una Exponencial con $\lambda=1/\eta$.

---

## Curva de bañera: elegir según el mecanismo

<div class="columns">
<div>

<img class="diagram" src="images/bathtub-curve.svg" alt="Curva de bañera: la tasa de riesgo h(t) decrece en fallas tempranas, se estabiliza durante la vida útil y crece por desgaste">

</div>
<div>

| Weibull | Comportamiento de $h(t)$ | Lectura |
|---|---|---|
| $0<\beta<1$ | decreciente | fallas tempranas |
| $\beta=1$ | aproximadamente constante | caso exponencial |
| $\beta>1$ | creciente | desgaste |

<div class="bridge">
La distribución se elige por el mecanismo de falla y la evidencia disponible, no por comodidad algebraica.
</div>

</div>
</div>

<div class="small">
La curva de bañera es una composición conceptual de mecanismos. Una sola Weibull representa una tendencia y no necesariamente las tres fases a la vez.
</div>

---

## Ejemplo Weibull: batería

La vida de una batería sigue un modelo Weibull con $\beta=2$ y $\eta=5\ \text{a\~nos}$.

**Confiabilidad a $3\ \text{a\~nos}$:**

$$
R(3)=e^{-(3/5)^2}\approx0.6977
$$

**Riesgo instantáneo a $3\ \text{a\~nos}$:**

$$
h(3)=\frac{2}{5}\left(\frac{3}{5}\right)
=0.24\ \text{a\~no}^{-1}
$$

El riesgo aumenta con la edad porque $\beta>1$.

---

## Tiempo medio hasta la falla (MTTF)

MTTF proviene de *Mean Time To Failure* y representa la vida esperada de una unidad no reparable.

Para una variable no negativa:

$$
\mathrm{MTTF}=E[T]
=\int_0^\infty t f(t)\,dt
=\int_0^\infty R(t)\,dt
$$

Casos importantes:

$$
\mathrm{MTTF}_{\mathrm{Exponencial}}=\frac{1}{\lambda}
$$

$$
\mathrm{MTTF}_{\mathrm{Weibull}}
=\eta\,\Gamma\!\left(1+\frac{1}{\beta}\right)
$$

---

## Derivación visual de $E[T]=\int R(t)\,dt$

Para $T\ge0$ puede escribirse:

$$
T=\int_0^\infty \mathbf 1_{\{T>t\}}\,dt
$$

$$
\mathbf 1_{\{T>t\}}=
\begin{cases}
1, & \text{si el componente sigue vivo en }t,\\
0, & \text{si ya fall\'o antes de }t.
\end{cases}
$$

La integral suma todos los instantes en que el componente sigue vivo. Por eso recupera el tiempo de vida $T$.

---

## Derivación visual de $E[T]=\int R(t)\,dt$

Tomando esperanza:

$$
E[T]=\int_0^\infty E\!\left[\mathbf 1_{\{T>t\}}\right]dt
=\int_0^\infty P(T>t)\,dt
=\int_0^\infty R(t)\,dt
$$

Como el valor esperado de una indicadora es la probabilidad del evento, aparece $P(T>t)=R(t)$.

---

## Confiabilidad no es disponibilidad

| Métrica | Evento | ¿Incluye reparación? |
|---|---|---|
| $R(t)$ | no fallar durante $(0,t]$ | no durante la misión |
| $A(t)$ | estar operativo en el instante $t$ | sí |
| $A_\infty$ | fracción operacional a largo plazo | sí |

Un servicio reparable puede experimentar varias fallas y mantener alta disponibilidad si sus reparaciones son rápidas.

<div class="bridge">
La reparación se modelará explícitamente con una cadena de Markov en tiempo continuo (CTMC). No debe introducirse dentro de $R(t)$ sin redefinir la métrica.
</div>

---

## Estimación a partir de datos de falla

En la práctica, la tasa de falla $\lambda$ normalmente no se conoce.

Para estimarla, se observan $n$ componentes similares y se registra cuánto tiempo funciona cada uno antes de fallar:

$$
t_1,t_2,\ldots,t_n
$$

Si se supone un modelo exponencial:

$$
\widehat{\lambda}
=
\frac{\text{fallas observadas}}
{\text{tiempo total de funcionamiento}}
=
\frac{n}{\sum_{i=1}^{n}t_i}
$$

*El símbolo $\widehat{\ }$ indica que el valor fue estimado desde datos y aproxima un parámetro real desconocido.*

---

## Estimación del MTTF

Para el modelo exponencial:

$$
\mathrm{MTTF}=\frac{1}{\lambda}
$$

Por lo tanto:

$$
\widehat{\mathrm{MTTF}}
=
\frac{1}{\widehat{\lambda}}
=
\frac{\sum_{i=1}^{n}t_i}{n}
$$

Así, cuando todos los componentes fueron observados hasta fallar:

$$
\boxed{
\widehat{\mathrm{MTTF}}
=
\text{promedio de los tiempos de falla observados}
}
$$

La tasa estimada y el MTTF estimado resumen empíricamente los tiempos de vida registrados.

---

## Ejemplo: estimación desde una muestra

Cinco componentes fallaron después de:

$$
800,\ 1000,\ 1200,\ 900,\ 1100\ \mathrm h
$$

El tiempo total de funcionamiento fue:

$$
\sum_{i=1}^{5}t_i=5000\ \mathrm h
$$

Entonces:

$$
\widehat{\lambda}
=
\frac{5}{5000}
=
0.001\ \mathrm h^{-1}
$$

$$
\widehat{\mathrm{MTTF}}
=
\frac{1}{\widehat{\lambda}}
=
1000\ \mathrm h
$$

*A partir de la muestra, se estima una vida media de $1000\ \mathrm h$ bajo el modelo exponencial.*

---

## Antes de estimar: condiciones y cautelas

Las expresiones anteriores suponen que:

- los componentes son comparables
- operan bajo condiciones similares
- sus tiempos de vida son independientes
- la tasa de falla es aproximadamente constante
- todos fueron observados hasta fallar.

Si algunas unidades aún funcionan al terminar el estudio, existen **datos censurados**.

$$
\widehat{\lambda}
=
\frac{\text{fallas observadas}}
{\text{tiempo total observado de todas las unidades}}
$$

<div class="warn">
Los componentes que no han fallado también aportan información mediante el tiempo que permanecieron funcionando.
</div>

---

## Ejemplo

Un módulo tiene vida exponencial con media $4000\ \mathrm h$.

1. Determine $\lambda$.
2. Calcule $R(1000)$.
3. Calcule $P(1000<T\le1500)$.
4. Dado que sobrevivió $1000\ \mathrm h$, calcule la probabilidad de que sobreviva hasta $1500\ \mathrm h$.

---

## Solución: modelo e intervalo

$$
\lambda=\frac{1}{4000}=2.5\times10^{-4}\ \mathrm h^{-1}
$$

$$
R(1000)=e^{-1000/4000}=e^{-0.25}\approx0.7788
$$

$$
\begin{aligned}
P(1000<T\le1500)
&=F(1500)-F(1000)\\
&=R(1000)-R(1500)\\
&=e^{-0.25}-e^{-0.375}\\
&\approx0.0915
\end{aligned}
$$

---

## Solución: probabilidad condicional

$$
\begin{aligned}
P(T>1500\mid T>1000)
&=\frac{P(T>1500)}{P(T>1000)}\\
&=\frac{e^{-1500/4000}}{e^{-1000/4000}}\\
&=e^{-500/4000}\\
&\approx0.8825
\end{aligned}
$$

La simplificación depende de la falta de memoria de la distribución exponencial.

---

## Errores frecuentes

| Error | Corrección |
|---|---|
| Interpretar $h(t)$ como probabilidad | Es una tasa. Use un intervalo pequeño para aproximar probabilidad |
| Usar $1/\lambda$ sin revisar unidades | Si $\lambda$ está en $\mathrm h^{-1}$, la media queda en $\mathrm h$ |
| Asumir Exponencial por conveniencia | Justifique tasa aproximadamente constante |
| Confundir $P(T>t)$ con $P(T\le t)$ | Son $R(t)$ y $F(t)=1-R(t)$ |
| Confundir MTTF con una garantía | Es un promedio, no un mínimo |

---

## Puente hacia la arquitectura

Hasta ahora se modeló **un componente**. Un servicio real depende de varios.

<img class="diagram" src="images/serie-paralelo.png" alt="Comparación de componentes conectados en serie y en paralelo">

---

## Puente hacia la arquitectura

La siguiente clase responderá:

$$
R_1(t),\ldots,R_n(t)
\quad\Longrightarrow\quad
R_{\mathrm{sistema}}(t)
$$

El notebook `codigo/reliability_models.ipynb` permite variar tasas, Weibull y configuraciones $k$-de-$n$.

---

## Cierre

- $R(t)$ cuantifica la supervivencia de una misión definida.
- $F(t)$, $f(t)$, $R(t)$ y $h(t)$ son representaciones coherentes de $T$.
- Exponencial implica riesgo constante. Weibull permite riesgo variable.
- $\mathrm{MTTF}=\int_0^\infty R(t)\,dt$.
- Confiabilidad y disponibilidad no son intercambiables.

---

## Referencias

- K. S. Trivedi y A. Bobbio, *Reliability and Availability Engineering: Modeling, Analysis, and Applications*, Cambridge University Press, 2017, caps. 1-3.
- K. S. Trivedi, *Probability and Statistics with Reliability, Queuing, and Computer Science Applications*, 2.ª ed., Wiley, 2002, caps. 3-4.
- J. M. Martínez, *TEL211: Reliability as a Function of Time* y *Expectation*, material histórico USM.
