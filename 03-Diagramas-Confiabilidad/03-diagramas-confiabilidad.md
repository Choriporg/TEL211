---
marp: true
math: mathjax
paginate: true
style: |
  section {
    font-size: 27px;
    line-height: 1.35;
  }
  section.lead {
    text-align: center;
  }
  section.lead h1 {
    font-size: 2.1em;
  }
  h2, h3 {
    color: #12394f;
  }
  code {
    font-size: 0.84em;
  }
  pre {
    border-left: 5px solid #2f6f9f;
    padding: 0.75em;
  }
  table {
    font-size: 0.77em;
  }
  .columns {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 32px;
    align-items: start;
  }
  .callout {
    background: #eef6fb;
    border-left: 6px solid #2f6f9f;
    border-radius: 6px;
    padding: 0.7em 0.9em;
  }
  .bridge {
    background: #f7f8fa;
    border-left: 6px solid #6b7280;
    border-radius: 6px;
    padding: 0.65em 0.9em;
  }
  .warn {
    background: #fff4df;
    border-left: 6px solid #b7791f;
    border-radius: 6px;
    padding: 0.65em 0.9em;
  }
  .example-space {
    background: #eef8f1;
    border-left: 6px solid #2f855a;
    border-radius: 6px;
    padding: 0.65em 0.9em;
  }
  .small {
    font-size: 0.82em;
  }
  img.diagram {
    display: block;
    margin: 0 auto;
    max-height: 430px;
    max-width: 100%;
  }
  img.diagram-small {
    display: block;
    margin: 0 auto;
    max-height: 315px;
    max-width: 100%;
  }
  img.diagram-large {
    display: block;
    margin: 0 auto;
    max-height: 560px;
    max-width: 108%;
  }
  img.screenshot,
  img.example-img {
    display: block;
    margin: 0 auto;
    max-width: 100%;
    max-height: 330px;
    border: 1px solid #d0d7de;
  }
  section.compact {
    font-size: 23px;
  }
---
<!-- _class: lead -->

# TEL211
## Disponibilidad y Rendimiento de Sistemas TIC
### Diagramas de bloques de confiabilidad (RBD)

Patricio Olivares R.  
Universidad Técnica Federico Santa María

---

## Propósito de la clase

Partir de las confiabilidades temporales $R_i(t)$ de los componentes y transformarlas en la confiabilidad de una arquitectura completa mediante un **diagrama de bloques de confiabilidad** (RBD, *Reliability Block Diagram*).

<div class="callout">
La distribución de cada componente responde "¿sobrevive la misión?". El RBD agrega "¿qué combinación de componentes permite que el sistema cumpla su función?".
</div>

---

## Ruta

En esta clase pasaremos de la confiabilidad de un componente a la de un sistema, primero con estructuras reducibles y luego con redes más generales:

1. **Motivación:** ¿Por qué necesitamos modelar sistemas complejos?
2. **Concepto de RBD:** Bloques, conexiones y significado
3. **Configuración serie:** Confiabilidad del sistema
4. **Configuración paralelo:** Redundancia y mejora
5. **Dependencias estructurales:** límites de la redundancia
6. **Sistemas $k$ de $n$ y redundancia modular triple (TMR):** redundancia con mayoría
7. **Sistemas no reducibles:** condicionamiento y conjuntos mínimos

---

## ¿Qué es un Diagrama de Confiabilidad?

Un **RBD** es una representación gráfica donde:

- Cada **bloque** representa un componente o subsistema del sistema
- El **estado del bloque** (funcionando/fallado) afecta el sistema
- La **conexión entre bloques** define la lógica de funcionamiento, no necesariamente la conexión física

El sistema funciona si existe un camino de bloques operativos entre la entrada y la salida. Por eso, un RBD representa la **estructura lógica** de éxito o falla del sistema. Los modelos temporales vistos anteriormente entregan $R_i(t)$ para cada componente, por ejemplo mediante Exponencial o Weibull.

<div class="bridge">
El RBD combina esas confiabilidades según la arquitectura del sistema.
</div>

---

## Elementos Básicos de un RBD

### Componentes

Cada bloque representa el evento $E_i=$ "el componente $i$ cumple su función durante la misión". Su probabilidad es la confiabilidad temporal que se obtuvo en la unidad anterior:

$$
P(E_i)=R_i(t)
$$

Las reglas de producto y complemento que siguen suponen independencia entre los eventos $E_i$.

---

## Elementos Básicos de un RBD

<div class="columns">
<div class="warn">
<strong>Dependencia</strong>
<p>Las ramas paralelas representan alternativas lógicas, pero no garantizan independencia. Las fallas de causa común requieren un modelo adicional.</p>
</div>

<div class="example-space">
<strong>Ejemplo TIC</strong>
<p>Dos servidores pueden compartir un switch, alimentación o almacenamiento. Si falla el recurso común, ambas ramas fallan y el paralelo sobreestima la confiabilidad.</p>
</div>
</div>

---

## Elementos Básicos de un RBD

### Conexiones:
- **Serie:** Todos deben funcionar
- **Paralelo:** Al menos uno debe funcionar
- **Mixto:** Combinación de ambas

---

## Ejemplo de RBD: switch y servidores redundantes

<img class="diagram" src="images/rbd-ejemplo-basico.png" alt="Comparación entre una arquitectura TIC y su representación como RBD">

<div class="small">
El sistema real muestra componentes y conexiones. El RBD abstrae esos detalles y conserva la lógica de funcionamiento: debe existir un camino operativo entre entrada y salida.
</div>

---

## Configuración en Serie

**Regla:** El sistema funciona si y solo si **todos** los componentes funcionan.

El evento de éxito es la intersección de los eventos de funcionamiento. Bajo independencia, la probabilidad de esa intersección se obtiene multiplicando las confiabilidades individuales.

### Cálculo:

$$
R_{\mathrm{sistema}}(t)=R_1(t)R_2(t)\cdots R_n(t)=\prod_i R_i(t)
$$

<div class="callout">
Cada componente obligatorio agrega una oportunidad de falla. Por eso, la confiabilidad del sistema serie no es mayor que la del componente menos confiable.
</div>

---

## Configuración en Serie: Ejemplo

<img class="diagram" src="images/rbd-serie-ejemplo.png" alt="Diagrama RBD de tres componentes en serie">

<div class="small">
Las tres funciones son obligatorias. El sistema funciona solo si el camino completo permanece operativo.
</div>

---

## Configuración en Serie: Ejemplo

Tres componentes con confiabilidades:
- $R_1=0.95$
- $R_2=0.90$  
- $R_3=0.85$

**Cálculo:**

$$
R_{\mathrm{sistema}}=0.95\times0.90\times0.85=0.72675\approx72.7\%
$$

<div class="warn">
Aún con componentes de alta confiabilidad, el sistema en serie puede tener confiabilidad significativamente menor.
</div>

El resultado muestra el efecto acumulativo: la misión sólo se completa si los tres componentes sobreviven.

---

## Configuración en Paralelo (Redundancia)

**Regla:** El sistema funciona si **al menos uno** de los componentes funciona.

Aquí resulta más directo calcular el evento contrario: que fallen todas las ramas. Su complemento es el éxito del sistema.

### Cálculo (para $n$ componentes):

$$
R_{\mathrm P}(t)=1-\prod_{i=1}^{n}[1-R_i(t)]
$$

Para $n=2$ se recupera la expresión $1-(1-R_1(t))(1-R_2(t))=1-F_1(t)F_2(t)$.

---

## Configuración en Paralelo: Ejemplo

<img class="diagram" src="images/rbd-paralelo-ejemplo.png" alt="Diagrama RBD de dos ramas redundantes en paralelo">

<div class="small">
Las dos ramas están activas y cualquiera puede sostener la salida. La misión falla solo si fallan ambas.
</div>

---

## Configuración en Paralelo: Ejemplo

Dos componentes idénticos con $R=0.90$:

**Cálculo:**

$$
R_{\mathrm{sistema}}=1-(1-0.90)(1-0.90)=1-0.10\times0.10=0.99=99\%
$$

<div class="callout">
La redundancia mejora significativamente la confiabilidad: de 90% a 99% con solo un componente adicional.
</div>

---

## Configuración en Paralelo: Ejemplo

La mejora ocurre porque ahora una falla individual no termina la misión. Deben fallar ambas ramas.

<div class="bridge">
Este cálculo representa redundancia <strong>activa</strong>. Las ramas operan simultáneamente y sus fallas se modelan como independientes. 
</div>

---

## Comparación Serie vs Paralelo

| Configuración | Confiabilidad | Uso típico |
|---|---|---|
| **Serie** | No mayor que el componente menos confiable | Cadena obligatoria de funciones |
| **Paralelo** | No menor que cada rama | Redundancia crítica |

**Intuición:** en serie se acumulan puntos únicos de falla. En paralelo se requiere que fallen todas las ramas para perder la función.

<div class="bridge">
La comparación separa dos decisiones de diseño: eliminar puntos obligatorios de falla o agregar alternativas para una función crítica.
</div>

---

## Dependencia estructural: límites de la redundancia

Dos servidores pueden estar conectados en paralelo y, aun así, compartir un switch, una fuente de alimentación o un sistema de almacenamiento:

```text
Servidor A ──┐
             ├── Recurso común ── Salida
Servidor B ──┘
```

Si falla el recurso común, las dos ramas quedan fuera de servicio simultáneamente. Por lo tanto, las ramas no son independientes y la redundancia aparente sobreestima la confiabilidad.

<div class="warn">
El cálculo de paralelo sólo es válido si el recurso común se incluye como bloque propio o si sus fallas se modelan explícitamente.
</div>

---

## Redundancia de sistema y de componentes

Replicar el sistema completo y replicar cada componente por separado no son la misma decisión de diseño:

- **Redundancia de sistema:** se duplican trayectorias completas y luego se conectan en paralelo.
- **Redundancia de componentes:** se agregan alternativas dentro de cada función obligatoria.

Las dos estrategias producen RBD distintos. Además, los recursos compartidos, como switches, fuentes o almacenamiento, pueden convertirse en puntos únicos de falla y reducir el beneficio esperado.

<div class="bridge">
Antes de comparar alternativas, identifique qué función se está duplicando y qué elementos siguen siendo obligatorios para todas las trayectorias.
</div>

---

## Sistemas Mixtos

### Ejemplo: serie y paralelo

El componente $1$ es obligatorio y luego existen tres alternativas redundantes $2a$, $2b$ y $2c.$

La estrategia es reducir primero el bloque interno más simple y usar su resultado como un único bloque dentro de la estructura restante.

**Pasos de cálculo:**

1. Calcular paralelismo: $R_{\mathrm{paralelo}}=1-(1-R_{2a})(1-R_{2b})(1-R_{2c})$
2. Calcular serie total: $R_{\mathrm{sistema}}=R_1R_{\mathrm{paralelo}}$

---

## Sistemas Mixtos

### Ejemplo: serie y paralelo

<img class="diagram" src="images/rbd-mixto-ejemplo.png" alt="Diagrama RBD con un bloque en serie y tres ramas en paralelo">

<div class="small">
El bloque 1 es obligatorio y está en serie con el subsistema paralelo formado por 2a, 2b y 2c.
</div>

---

## Sistemas Mixtos

### Ejemplo: serie y paralelo

Componentes: $R_1=0.95$, $R_{2a}=R_{2b}=R_{2c}=0.80$

**Paso 1: Paralelo**

$$
R_{\mathrm{paralelo}}=1-(1-0.80)^3=1-0.20^3=0.992
$$

**Paso 2: Serie total**

$$
R_{\mathrm{sistema}}=0.95\times0.992=0.9424\approx94.2\%
$$

La redundancia protege la función asociada a $2a$, $2b$ y $2c$, pero el componente $1$ continúa siendo obligatorio y limita la confiabilidad total.

---

## Sistemas mixtos: segundo ejemplo

### Serie y paralelo dentro del mismo RBD

Este ejemplo combina una función obligatoria antes y después de una función redundante. El sistema requiere que funcionen $A$ y $C$, además de al menos una de las ramas $B_1$ y $B_2$.

<img class="diagram" src="images/rbd-mixto.png" alt="RBD mixto con los bloques A y C en serie y las ramas B1 y B2 en paralelo">

---

## Sistemas mixtos: estrategia de cálculo

### Reducir de adentro hacia afuera

Primero se reduce el bloque paralelo y después se calcula la serie restante.

$$
R_B=1-(1-R_{B_1})(1-R_{B_2})
$$

$$
R_{\mathrm{sistema}}=R_A R_B R_C
$$

<div class="callout">
Reemplazar el paralelo por un bloque equivalente permite aplicar luego la regla de producto de los elementos que quedan en serie.
</div>

---

## Importancia y sensibilidad de los componentes

Una vez calculado $R_{\mathrm{s}}(t)$, podemos preguntar cuánto cambia la confiabilidad del sistema si mejora la confiabilidad de un componente. La **importancia de Birnbaum** se define como:

$$
I_i^B(t)=\frac{\partial R_{\mathrm{s}}(t)}{\partial R_i(t)}
$$

Para derivar esta medida, se toma $R_i(t)$ como variable y se consideran constantes las confiabilidades de los demás bloques.

---

## Importancia de Birnbaum: derivación

En cada arquitectura se separa el factor que contiene $R_i(t)$ y luego se deriva.

### Serie

$$
R_{\mathrm{s}}(t)=R_i(t)\prod_{j\neq i}R_j(t)
\quad\Longrightarrow\quad
I_i^B(t)=\prod_{j\neq i}R_j(t)
$$

### Paralelo

$$
R_{\mathrm{s}}(t)=1-[1-R_i(t)]\prod_{j\neq i}[1-R_j(t)]
$$

Al derivar, el signo del complemento y el de $1-R_i(t)$ se cancelan.

$$
I_i^B(t)=
\frac{\partial}{\partial R_i(t)}
\left\{1-[1-R_i(t)]\prod_{j\neq i}[1-R_j(t)]\right\}
=\prod_{j\neq i}[1-R_j(t)]
$$

---

## Cómo interpretar la importancia

- **Serie:** mejorar el componente $i$ ayuda solo si los demás bloques funcionan. Por eso su importancia es el producto de sus confiabilidades.
- **Paralelo:** mejorar $i$ ayuda cuando fallan todas las demás ramas. Por eso su importancia es el producto de sus probabilidades de falla.

Para comparar alternativas, priorice los componentes con mayor $I_i^B(t)$.

---

## MTTF de un sistema no reparable

El MTTF es la vida esperada hasta la **primera falla**. Para un componente es el área bajo $R(t)$. Para un sistema se aplica la misma idea, pero usando la confiabilidad obtenida desde su RBD:

$$
\mathrm{MTTF}_{\mathrm{s}}=\int_0^\infty R_{\mathrm{s}}(t)\,dt
$$

Primero se construye $R_{\mathrm{s}}(t)$ con la arquitectura. Luego se integra esa curva.

---

## Ejemplo: MTTF desde la confiabilidad del sistema

Suponga que el RBD de un sistema no reparable entrega:

$$
R_{\mathrm{s}}(t)=e^{-0.002t}
$$

Entonces su MTTF es el área bajo esa curva:

$$
\mathrm{MTTF}_{\mathrm{s}}
=\int_0^\infty e^{-0.002t}\,dt
=\frac{1}{0.002}
=500\ \mathrm{h}
$$

La arquitectura ya está incorporada en $R_{\mathrm{s}}(t)$. La integración transforma esa confiabilidad en tiempo esperado.

---

## MTTF con RBD: caso serie

Con distribución exponencial, la tasa de falla del sistema serie es la suma de las tasas de sus componentes:

$$
\lambda_{\mathrm{sistema}}=\sum_i \lambda_i,\qquad
\mathrm{MTTF}_{\mathrm{sistema}}=\frac{1}{\lambda_{\mathrm{sistema}}}
$$

---

## MTTF con RBD: caso paralelo

Considere $n$ componentes idénticos, independientes, activos y exponenciales, cada uno con tasa $\lambda$. El sistema falla cuando fallan todas las ramas:

$$
R_{\mathrm{sistema}}(t)=1-[1-e^{-\lambda t}]^n
$$

Si $\mathrm{MTTF}_{\mathrm{componente}}=1/\lambda$ y $H_n$ es el número armónico:

$$
\mathrm{MTTF}_{\mathrm{sistema}}
=\int_0^\infty R_{\mathrm{sistema}}(t)\,dt
=\frac{H_n}{\lambda}
=H_n\,\mathrm{MTTF}_{\mathrm{componente}},
\qquad
H_n=\sum_{i=1}^{n}\frac{1}{i}
$$

Para $n=2$:

$$
R_{\mathrm{sistema}}(t)=2e^{-\lambda t}-e^{-2\lambda t}
$$

$$
\mathrm{MTTF}_{\mathrm{sistema}}
=\frac{2}{\lambda}-\frac{1}{2\lambda}
=1.5\,\mathrm{MTTF}_{\mathrm{componente}}
$$

<div class="small">
El MTTF cuantifica tiempo esperado. No reemplaza la confiabilidad requerida para una misión específica.
</div>

---

## Sistema $k$ de $n$

El paralelo es el caso "al menos uno". Un sistema $k$ de $n$ generaliza esa lógica. Funciona cuando al menos $k$ de sus $n$ componentes idénticos e independientes funcionan.

Si cada componente tiene confiabilidad $R(t)$, se suman los casos mutuamente excluyentes de exactamente $k$, $k+1$, ..., $n$ componentes operativos:

$$
R_{k|n}(t)=\sum_{j=k}^{n}\binom{n}{j}
[R(t)]^j[1-R(t)]^{n-j}
$$

Ejemplos: $1$ de $n$ es paralelo. $n$ de $n$ es serie. $2$ de $3$ es redundancia modular triple.

---

## Redundancia modular triple (TMR) con votador imperfecto

La TMR es el caso $2$ de $3$. El votador acepta la salida de la mayoría. Por eso se suman los casos de exactamente dos módulos correctos y de tres módulos correctos:

$$
R_{2|3}(t)=3R(t)^2[1-R(t)]+R(t)^3
=3R(t)^2-2R(t)^3
$$

Si el votador independiente tiene confiabilidad $R_v(t)$:

$$
R_{\mathrm{TMR}}(t)=R_v(t)\,[3R(t)^2-2R(t)^3]
$$

Con un votador perfecto, $R_{2|3}(t)>R(t)$ si y solo si $R(t)>0.5$. Con un votador imperfecto, se debe comparar $R_v(t)[3R(t)^2-2R(t)^3]$ con $R(t)$: la redundancia sólo conviene si el resultado total supera al módulo simple.

<div class="warn">
El modelo supone fallas independientes y que "funcionar" equivale a entregar una salida correcta. La redundancia no elimina componentes críticos. Un votador único continúa siendo un punto único de falla.
</div>

---

## Componentes no idénticos en un sistema $2$ de $3$

Cuando los módulos no tienen la misma confiabilidad ya no basta una distribución binomial única. Se mantienen, sin embargo, los mismos casos mutuamente excluyentes de exactamente dos y de tres éxitos:

$$
\begin{aligned}
R_{2|3}={}&R_1R_2(1-R_3)+R_1R_3(1-R_2)\\
&+R_2R_3(1-R_1)+R_1R_2R_3
\end{aligned}
$$

Al simplificar:

$$
R_{2|3}=R_1R_2+R_1R_3+R_2R_3-2R_1R_2R_3
$$

---

## Condicionamiento: sistemas no reducibles

Las reducciones serie y paralelo dejan de bastar cuando las ramas se cruzan. Considere la red puente de cinco componentes, donde el componente $3$ conecta las dos ramas:

```text
Entrada ──[1]──●──[2]── Salida
                │
               [3]
                │
Entrada ──[4]──●──[5]── Salida
```

El estado de $C_3$ divide todos los escenarios posibles en dos casos disjuntos y exhaustivos. Al fijarlo como operativo o fallado, cada caso sí se vuelve serie y paralelo:

| Estado de $C_3$ | RBD reducido | Confiabilidad condicionada |
|---|---|---|
| Fallado | $[1]-[2]$ en paralelo con $[4]-[5]$ | $R_{S\mid C^c}=1-(1-R_1R_2)(1-R_4R_5)$ |
| Operativo | $[1]\parallel[4]$ en serie con $[2]\parallel[5]$ | $R_{S\mid C}=[1-(1-R_1)(1-R_4)][1-(1-R_2)(1-R_5)]$ |

---

## Puente: combinación de los casos

En las expresiones siguientes, $C$ denota el evento "$C_3$ funciona". La probabilidad total pondera la confiabilidad de cada RBD reducido por la probabilidad de su condición:

$$
R_{\mathrm{s}}=P(S\mid C)P(C)+P(S\mid C^c)P(C^c)
$$

$$
R_{\mathrm{s}}=R_C\,R_{\mathrm{s}\mid C}
+(1-R_C)R_{\mathrm{s}\mid C^c}
$$

- Si $C$ funciona, reemplácelo por una conexión perfecta.
- Si $C$ falla, elimínelo.
- Reduzca los dos RBD resultantes.

El mejor componente para condicionar es el que simplifica ambos casos. La enumeración de los $2^n$ estados puede verificar redes pequeñas, pero crece rápidamente. El condicionamiento evita esa explosión al resolver subproblemas serie y paralelo.

---

## Conjuntos mínimos de camino y de corte

- Un **conjunto mínimo de camino** reúne los componentes cuya operación conjunta garantiza un camino de éxito entre la entrada y la salida.
- Un **conjunto mínimo de corte** reúne los componentes cuya falla conjunta provoca la falla del sistema.

Estas representaciones resumen la misma lógica desde dos miradas: caminos para el éxito y cortes para la falla. Preparan la transición al árbol de fallas de la siguiente sección.

<div class="warn">
Si los caminos o cortes comparten componentes, sus eventos no son independientes y no se deben multiplicar directamente. Use condicionamiento, inclusión y exclusión u otro método que trate la dependencia.
</div>

---

<!-- diagram-slide: fault-tree-and-or -->

## Árbol de fallas (FT): compuertas Y (AND) / O (OR)

Mientras el RBD busca un camino de éxito, el árbol de fallas parte del evento no deseado y lo descompone en causas. Una compuerta OR basta con una causa. Una AND exige que ocurran todas.

<img class="diagram" src="images/fault-tree-and-or.png" alt="Árbol de fallas con evento superior y compuertas AND y OR">

<div class="small">
<strong>Lectura:</strong> FT proviene de <em>Fault Tree</em>. Las compuertas muestran la lógica causal que conduce al evento superior de falla.
</div>

---

<!-- diagram-slide: rbd-vs-ft -->

## RBD frente a FT: misma arquitectura, dos preguntas

Ambos modelos pueden representar la misma arquitectura, pero cambian el punto de partida y la pregunta que se responde.

<img class="diagram" src="images/rbd-vs-ft.png" alt="Comparación lado a lado entre RBD y Fault Tree">

<div class="small">
<strong>Lectura:</strong> RBD pregunta qué debe funcionar. FT pregunta qué combinación de eventos produce la falla. No se debe interpretar la forma geométrica de una compuerta FT como si fuera una conexión física.
</div>

<div class="warn">
Si el mismo componente aparece en más de una rama del árbol, representa un único evento repetido. No debe contarse como si fueran fallas independientes distintas. Su dependencia debe tratarse explícitamente.
</div>

---

## Errores Frecuentes

Antes de aceptar un resultado, revise la lógica funcional, el complemento usado y el supuesto de independencia:

| Síntoma | Causa probable |
|---|---|
| Confundir serie con paralelo en diagrama | No identificar correctamente dependencia lógica |
| Omitir el complemento al calcular la falla de todas las ramas | Tomar el producto de fallas como confiabilidad del paralelo |
| Ignorar dependencia entre componentes | Asumir independencia cuando existen correlaciones |

<div class="warn">
Los RBD describen la lógica estructural del sistema. Las reglas de producto, complemento y reducción de esta clase requieren eventos de funcionamiento independientes. Las correlaciones no consideradas pueden invalidar el cálculo.
</div>

---

## Ejercicio propuesto:

Este ejercicio integra las dos reducciones básicas y una decisión de diseño: mejorar una rama redundante o eliminar un punto único de falla.

Dado un sistema con:
- dos servidores independientes en paralelo, cada uno con $R=0.95$
- un switch independiente en serie con $R_{\mathrm{sw}}=0.98$.

**Preguntas:**
1. Dibuje el RBD del sistema
2. Calcule la confiabilidad total
3. Compare aumentar el switch a $0.99$ con aumentar cada servidor a $0.96$.

<div class="example-space">
Espacio para desarrollar cálculos y discutir prioridades de mejora en diseño.
</div>

---

## Solución del ejercicio

Confiabilidad del bloque de servidores:

$$
R_{\mathrm{srv}}=1-(1-0.95)^2=0.9975
$$

Sistema original:

$$
R_{\mathrm{s}}=0.98(0.9975)=0.97755
$$

Alternativas:

$$
R_{\mathrm{s}}^{(\mathrm{sw})}=0.99(0.9975)=0.987525
$$

$$
R_{\mathrm{s}}^{(\mathrm{srv})}=0.98[1-(1-0.96)^2]=0.978432
$$

Los incrementos son $\Delta R_{\mathrm{sw}}=0.987525-0.97755=0.009975$ y $\Delta R_{\mathrm{srv}}=0.978432-0.97755=0.000882$.

Mejorar el switch produce mayor beneficio porque participa obligatoriamente en toda trayectoria de éxito. La mejora adicional de una rama ya redundante tiene menor impacto marginal.

---

<!-- sequence-bridge -->

## Continuidad: de estructura a estados

- RBD y FT representan combinaciones estáticas de éxito o falla para una misión o instante dado.
- No describen de forma natural el orden de fallas, reparaciones ni estados degradados.
- Cuando la pregunta pasa de "qué estructura permite operar" a "cómo cambia el sistema en el tiempo", se requiere un modelo de estados.
- La siguiente unidad introduce procesos estocásticos y cadenas de Markov en tiempo discreto (DTMC) para representar esa evolución.

<div class="bridge">
Los RBD responden "qué componentes deben funcionar". Las cadenas de Markov conservan esa lógica funcional, pero agregan "cómo cambia el sistema entre estados".
</div>

---

## Cierre

Al terminar esta clase:

- Comprende la estructura básica de un **RBD**
- Puede calcular confiabilidad para sistemas **serie**, **paralelo** y **mixtos**
- Entiende el valor de la **redundancia** en diseño
- Sabe identificar cuándo una red exige condicionamiento o un análisis de fallas común

<div class="callout">
Los RBD transforman confiabilidades individuales en confiabilidad de sistema. La clave es traducir correctamente la función requerida, la arquitectura y sus supuestos antes de calcular.
</div>

---

## Referencias

- K. S. Trivedi y A. Bobbio, *Reliability and Availability Engineering: Modeling, Analysis, and Applications*, Cambridge University Press, 2017, caps. 4 y 6.
- K. S. Trivedi, *Probability and Statistics with Reliability, Queuing, and Computer Science Applications*, 2.ª ed., Wiley, 2002, cap. 4.
- J. M. Martínez, *TEL211: Reliability Block Diagrams*, material histórico USM.
