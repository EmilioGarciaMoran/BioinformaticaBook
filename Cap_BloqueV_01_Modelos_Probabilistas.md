# CAPÍTULO 1: MODELOS PROBABILISTAS EN BIOLOGÍA DE SISTEMAS

---

## INTRODUCCIÓN: ANTES DEL MODELO HAY UN CAPÍTULO

### Un Modelo No Es La Realidad Simplificada

La tentación es definir un modelo como "una versión simplificada de la realidad".

Eso es reconfortante pero falso.

Un modelo es una **conversación parcial con los datos**. Es el espacio de preguntas que decides hacer. El lenguaje que eliges para nombrar lo que ves.

Antes de cualquier algoritmo, antes de cualquier ecuación, existe una decisión más profunda: **qué tipo de mundo estás dispuesto a considerar plausible**.

Eso no es simplificación. Es definición del espacio de lo pensable.

### El Ejemplo Clínico

Un médico ve fiebre y tos en un paciente.

¿Qué modelo usa?

- **Modelo A:** "Infección viral o bacteriana"
- **Modelo B:** "Tuberculosis, neumonía, gripe, COVID, o contaminante inhalado"
- **Modelo C:** Capítulo completo de medicina interna con árboles de decisión, marcadores biológicos, y patrones temporales

Los tres son "modelos". Pero no son simplificaciones del mismo objeto. Son **mundos distintos donde la misma observación significa cosas diferentes**.

El modelo no simplifica. El modelo decide qué puede existir.

---

## 1. EL TRILEMA DEL MODELADO

### 1.1 Tres Dominios, No Uno

En la construcción de modelos probabilistas, hay tres componentes irreducibles que interactúan:

**I. El Dominio de la Representación (Espacio de Estados)**

Qué puede ser nombrado. Qué variables existen en el modelo.

**II. El Componente Estructural (Prior)**

Qué estructuras son plausibles antes de ver datos. La topología del conocimiento previo.

**III. El Componente Empírico (Likelihood)**

Cómo se mide la coherencia entre estructura propuesta y datos observados.

Ninguno de los tres es opcional. Ninguno se deduce de los otros.

### 1.2 Por Qué Esto No Es Obvio

La mayoría de cursos de estadística empiezan con:

> "Tenemos datos X y queremos estimar parámetro θ"

Pero eso **ya asume**:
- Que θ es una variable pensable (Dominio I)
- Que θ tiene ciertos valores más probables que otros antes de ver X (Dominio II)
- Que existe una función que conecta X con θ (Dominio III)

Todo eso son decisiones, no hechos.

---

## 2. DOMINIO I: EL ESPACIO DE REPRESENTACIÓN

### 2.1 Lo Que No Se Nombra No Existe

Un modelo probabilista trabaja sobre un **espacio de estados** Ω.

Si un fenómeno no está en Ω, su probabilidad no es cero. **No está definida**.

**Ejemplo:**

Estás modelando la expresión génica de un organismo con 20,000 genes.

Tu espacio Ω contiene variables para cada gen: $g_1, g_2, \ldots, g_{20000}$.

Si hay un gen 20,001 que no conocías (o que decidiste ignorar), el modelo **no puede inferir nada sobre él**. No porque sea improbable, sino porque no existe en el lenguaje del modelo.

### 2.2 El Espacio Define Los Límites

La probabilidad solo fluye dentro de Ω.

Formalmente:

$$\sum_{x \in \Omega} P(x) = 1$$

Todo lo que no está en Ω tiene probabilidad **indefinida**, no nula.

**Consecuencia:**

La pregunta "¿es correcto mi modelo?" no tiene sentido sin especificar primero "¿qué estoy dejando fuera?".

### 2.3 Ejemplo Biológico: Gene Ontology

En bioinformática, usamos **Gene Ontology (GO)**: una jerarquía de términos para describir funciones génicas.

Si un gen no tiene anotación GO, desde el punto de vista del análisis estadístico, ese gen "no hace nada".

No es que realmente no haga nada. Es que el lenguaje del modelo (GO) no tiene palabras para describir su función.

**Esto es Dominio I en acción:** Los límites del modelo son los límites del mundo modelable.

---

## 3. DOMINIO II: EL COMPONENTE ESTRUCTURAL (PRIOR)

### 3.1 Qué Es El Prior (Sin Misticismo)

El prior $P(\theta)$ no es una "opinión subjetiva".

Es la codificación de **restricciones estructurales** que impone la realidad antes de ver datos concretos.

**Ejemplos de restricciones:**

- **Físicas:** Probabilidades deben sumar 1. Tasas no pueden ser negativas.
- **Biológicas:** Genes homeólogos suelen tener expresión correlacionada.
- **Evolutivas:** Secuencias cercanas filogenéticamente comparten estructura.
- **Termodinámicas:** Procesos irreversibles no retroceden espontáneamente.

Ninguna de estas es "subjetiva". Son propiedades del sistema que conocemos **antes de medir esta muestra específica**.

### 3.2 El Prior Como Invarianza

Otra forma de ver el prior:

> El prior codifica lo que **no cambia** entre experimentos.

Si secuencias genomas de 1000 individuos humanos, cada uno tiene variantes únicas (datos). Pero todos comparten:
- 46 cromosomas
- ~20,000 genes
- Mismo orden genómico (salvo reordenamientos raros)

Eso es estructura. Eso es el prior.

### 3.3 Por Qué El Prior No Es Opcional

Incluso cuando no lo escribes explícitamente, **tu modelo tiene un prior implícito**.

**Ejemplo: Regresión Lineal Clásica**

$$y = \beta_0 + \beta_1 x + \epsilon$$

Parece que no hay prior. Pero implícitamente asumes:

- $\beta_0, \beta_1 \in \mathbb{R}$ (pueden ser cualquier número) → prior uniforme "impropio"
- $\epsilon \sim N(0, \sigma^2)$ → estructura de error gaussiana
- Relación **lineal** (no cuadrática, no exponencial)

Todo eso es prior. Solo que no lo llamaste así.

### 3.4 El Prior Regula Complejidad

Cuando el prior es "débil" (uniforme, poco informativo):
- El modelo puede ajustarse mucho a los datos
- Riesgo de sobreajuste (overfitting)

Cuando el prior es "fuerte" (concentrado, informativo):
- El modelo resiste cambios bruscos
- Suaviza el ruido
- Pero puede ignorar señales reales si el prior está mal

**Esto no es defecto. Es la función del prior:**

Evitar que el modelo "se vuelva loco" con poco datos.

---

## 4. DOMINIO III: EL COMPONENTE EMPÍRICO (LIKELIHOOD)

### 4.1 Definición Operativa

La likelihood $\mathcal{L}(\theta | x) = P(x | \theta)$ responde:

> Suponiendo que el modelo con parámetros θ fuera cierto, ¿qué probabilidad habría de observar estos datos x?

**No es:**
- La probabilidad de que θ sea cierto (eso es el posterior)
- Una medida de "bondad" absoluta

**Es:**
- Una medida de **coherencia** entre hipótesis y observación

### 4.2 La Barra Condicional Fija El Mundo

En $P(x | \theta)$:

- θ está **fijado** (a la derecha de |)
- x es lo que varía (a la izquierda)

Lees: "Dado θ, ¿cómo de probable es x?"

Esto invierte la pregunta natural ("¿qué θ explica x?") en una computacionalmente manejable.

### 4.3 Ejemplo: Diagnóstico Médico

**Condición:** Tuberculosis (θ = TB)

**Datos observados:** Fiebre, tos crónica, pérdida de peso (x)

**Likelihood:**

$$P(\text{fiebre, tos, pérdida peso} | \text{TB})$$

Esto **no** dice "qué probabilidad hay de que sea TB dado que veo estos síntomas". 

Dice: "Si fuera TB, ¿qué tan probable sería ver exactamente estos síntomas?"

### 4.4 La Likelihood Como Topografía

La likelihood no es un número. Es una **función sobre el espacio de parámetros**.

Puedes recorrer θ y ver cómo sube o baja $\mathcal{L}(\theta | x)$.

**Visualización:**

```
        L(θ|x)
          ^
          |     *
          |    * *
          |   *   *
          |  *     *
          | *       *
          |*_________*___> θ
```

Algunos valores de θ hacen los datos muy probables (picos). Otros, improbables (valles).

### 4.5 Likelihood vs. Error (Conexión Algebraica)

Si asumes errores gaussianos:

$$\epsilon \sim N(0, \sigma^2)$$

Entonces:

$$\text{Maximizar likelihood} \equiv \text{Minimizar suma de errores al cuadrado}$$

**Por qué:**

La likelihood gaussiana es:

$$\mathcal{L} \propto \exp\left(-\frac{1}{2\sigma^2} \sum (y_i - \hat{y}_i)^2 \right)$$

Maximizar eso es equivalente a minimizar:

$$\sum (y_i - \hat{y}_i)^2$$

**Moraleja:**

El "ajuste" algebraico (mínimos cuadrados) y la likelihood estadística son dos caras de la misma moneda **cuando asumes normalidad**.

Si el error no es gaussiano, divergen.

---

## 5. MAXIMUM LIKELIHOOD ESTIMATION (MLE)

### 5.1 La Pregunta Central

Dados datos fijos $x_{\text{obs}}$, ¿qué parámetros θ hacen esos datos **más probables**?

$$\hat{\theta}_{\text{MLE}} = \arg\max_{\theta} P(x_{\text{obs}} | \theta)$$

### 5.2 Por Qué Funciona (Y Por Qué No Siempre)

**Ventajas:**
- Directo: buscas un máximo
- No necesitas especificar prior (aunque implícitamente usas uno uniforme)
- Funciona bien con muchos datos

**Limitaciones:**
- Puede sobreajustar con pocos datos
- No captura incertidumbre (da un punto, no una distribución)
- Sensible a outliers

### 5.3 Ejemplo: Parámetro de una Distribución Binomial

Lanzas una moneda 10 veces. Sale cara 7 veces.

**Modelo:** $X \sim \text{Binomial}(n=10, p)$

**Likelihood:**

$$\mathcal{L}(p | x=7) = \binom{10}{7} p^7 (1-p)^3$$

**MLE:**

Derivar respecto a p, igualar a cero:

$$\hat{p}_{\text{MLE}} = \frac{7}{10} = 0.7$$

Intuitivo: La proporción observada.

### 5.4 MLE En Filogenética

En filogenia, estimas:
- Topología del árbol T
- Longitudes de ramas b
- Parámetros de sustitución θ

MLE busca:

$$\hat{T}, \hat{b}, \hat{\theta} = \arg\max P(\text{MSA} | T, b, \theta)$$

**Problema:** El espacio de árboles es **inmenso** (superexponencial en número de taxa).

No puedes evaluar todos. Usas heurísticas (búsqueda greedy, algoritmos genéticos).

---

## 6. CRITERIOS DE SELECCIÓN DE MODELOS: AIC Y BIC

### 6.1 El Problema de la Complejidad

MLE **siempre premia modelos más complejos** (más parámetros).

Un modelo con 100 parámetros ajustará mejor 50 datos que uno con 5 parámetros.

Pero eso es **sobreajuste**. El modelo complejo "memoriza" el ruido.

### 6.2 AIC (Akaike Information Criterion)

$$\text{AIC} = 2k - 2 \ln(\hat{\mathcal{L}})$$

Donde:
- $k$ = número de parámetros
- $\hat{\mathcal{L}}$ = likelihood máxima

**Interpretación:**

Penaliza complejidad (término $2k$).

**Menor AIC → Mejor modelo** (balance ajuste/complejidad)

### 6.3 BIC (Bayesian Information Criterion)

$$\text{BIC} = k \ln(n) - 2 \ln(\hat{\mathcal{L}})$$

Donde:
- $n$ = número de observaciones

**Diferencia con AIC:**

Penaliza **más fuertemente** cuando $n$ es grande ($\ln(n) > 2$ para $n > 7$).

Favorece modelos más simples.

### 6.4 Cuándo Usar Cada Uno

| Criterio | Penalización | Filosofía | Uso típico |
|----------|--------------|-----------|------------|
| **AIC** | $2k$ | Predictiva | Maximizar capacidad de generalización |
| **BIC** | $k \ln(n)$ | Parsimonia | Seleccionar "verdadero" modelo generador |

En la práctica:
- Si $n$ pequeño → AIC y BIC parecidos
- Si $n$ grande → BIC penaliza más

### 6.5 Ejemplo: Modelos de Sustitución en Filogenética

Comparas modelos evolutivos:

- **JC69:** 0 parámetros (todas tasas iguales)
- **HKY85:** 2 parámetros (transiciones ≠ transversiones)
- **GTR:** 5 parámetros (todas las tasas distintas)

Calculas likelihood de cada uno en el MSA.

Calculas AIC/BIC.

**Resultado típico:**

- JC69: AIC alto (ajusta mal)
- GTR: likelihood alta pero penalización también → AIC intermedio
- HKY85: **AIC más bajo** → balance óptimo

---

## 7. INFERENCIA BAYESIANA: INTEGRACIÓN EN LUGAR DE OPTIMIZACIÓN

### 7.1 La Diferencia Fundamental

**MLE:** Encuentra un punto óptimo en el espacio de parámetros.

**Bayes:** Mantiene una **distribución** sobre todo el espacio de parámetros.

### 7.2 El Teorema de Bayes (Sin Misticismo)

$$P(\theta | x) \propto P(x | \theta) \cdot P(\theta)$$

**En palabras:**

La creencia actualizada sobre θ (posterior) es proporcional al producto de:
- Cómo de bien θ explica los datos (likelihood)
- Cuán plausible era θ antes de ver datos (prior)

### 7.3 Por Qué El Denominador No Importa (Casi Nunca)

La fórmula completa es:

$$P(\theta | x) = \frac{P(x | \theta) \cdot P(\theta)}{P(x)}$$

Donde:

$$P(x) = \int P(x | \theta) P(\theta) d\theta$$

**El problema:**

$P(x)$ requiere integrar sobre **todo** el espacio de θ. 

Eso es computacionalmente intratable para modelos complejos.

**La solución:**

En la práctica, $P(x)$ es una constante de normalización. No depende de θ.

Entonces:

$$P(\theta | x) \propto P(x | \theta) \cdot P(\theta)$$

El símbolo $\propto$ significa "proporcional a". 

Puedes trabajar sin calcular $P(x)$ explícitamente. Métodos como MCMC (Markov Chain Monte Carlo) explotan esto.

### 7.4 Bayes Como Proceso Histórico, No Geométrico

**Error conceptual común:**

Pensar en el prior y posterior como "puntos" en un plano cartesiano con origen (0,0).

**Realidad:**

Prior y posterior son **estados consecutivos** de conocimiento.

No hay origen. No hay punto neutro. Solo hay:

$$\text{Estado}_{n-1} \xrightarrow{\text{dato nuevo}} \text{Estado}_n$$

El prior es el conocimiento en el tiempo $t-1$.
El posterior es el conocimiento en el tiempo $t$.

### 7.5 Prior y Posterior Como Índices de un Array

Metáfora computacional:

```python
conocimiento = [estado_0, estado_1, estado_2, ...]

# Al llegar nuevo dato:
conocimiento[n] = actualizar(conocimiento[n-1], dato_nuevo)
```

No hay "reinicio". No hay simetría temporal. Solo avance.

### 7.6 El Horizonte Nunca Termina

**El eje Y (modelos/hipótesis):**

Es **finito** porque tú lo defines. Es tu lenguaje, tu espacio Ω.

**El eje X (datos):**

Es potencialmente **infinito**. La realidad sigue generando datos.

Siempre puedes:
- Medir más
- Añadir nueva escala
- Incluir otra variable

**Consecuencia:**

El posterior **nunca converge a "la verdad"**. Converge a una región habitable del espacio de hipótesis dado los datos hasta ahora.

---

## 8. LA EVIDENCIA MARGINAL: EL PRECIO DE SEGUIR CRECIENDO

### 8.1 Qué Es

La evidencia marginal $P(x)$ (la que normalmente ignoramos) responde:

> ¿Qué tan probable es observar estos datos bajo **cualquier** configuración posible del modelo?

Formalmente:

$$P(x) = \int P(x | \theta) P(\theta) d\theta$$

### 8.2 Por Qué Es Difícil

Requiere:
- Integrar sobre **todo** el espacio de θ
- Que puede ser alta dimensión (miles de parámetros)
- Con prior que puede no tener forma analítica cerrada

En filogenética, esto es:

$$P(\text{MSA}) = \sum_{\text{todos los árboles T}} P(\text{MSA} | T) P(T)$$

El número de árboles posibles crece como $(2n-3)!! \approx 1.5^n \cdot n!$.

Para 50 taxa: $\sim 10^{76}$ árboles (más que átomos en el universo observable).

**No way.**

### 8.3 Para Qué Sirve (Cuando La Puedes Calcular)

La evidencia marginal permite **comparar modelos**:

$$\text{Bayes Factor} = \frac{P(x | M_1)}{P(x | M_2)}$$

Si BF > 10 → Fuerte evidencia para $M_1$.

**Ventaja sobre AIC/BIC:**

No es una aproximación. Es la comparación bayesiana exacta (si puedes calcularla).

### 8.4 La Metáfora del Crecimiento

Añadir datos (nueva fila en MSA, nuevo locus) **no siempre mejora el modelo**.

Si el nuevo dato es inconsistente con la estructura previa:
- Likelihood de cada árbol sube menos de lo esperado
- Evidencia marginal puede **bajar**

**Esto es lo que significa "no compensa crecer".**

El modelo ya no puede sostener coherentemente toda la información.

---

## 9. APLICACIÓN: FILOGENIA BAYESIANA

### 9.1 El Orden Correcto

**Antes de abrir BEAST o MrBayes:**

1. **Capítulo de libro (biología):**
   - Qué tipo de historia evolutiva esperas
   - Qué conflictos son plausibles (recombinación, transferencia horizontal, duplicación)
   
2. **Árbol inicial razonable:**
   - Topología defendible (NJ, parsimonia)
   - No la "verdad", sino una hipótesis inicial sensata
   
3. **Definición del espacio taxa × loci:**
   - ¿Es fijo o extensible?
   - ¿Hasta dónde?
   - ¿Qué regiones del genoma son informativas?
   
4. **Modelo de sustitución:**
   - JC, HKY, GTR
   - +Γ (heterogeneidad de tasas)
   - +I (sitios invariantes)
   
5. **Priors:**
   - Distribución de longitudes de rama
   - Reloj molecular (estricto, relajado, libre)
   - Topología (uniforme sobre árboles, o sesgado por grupos monofiléticos conocidos)

**Solo entonces:**

6. Inferencia bayesiana (MCMC)
7. Diagnóstico de convergencia
8. Interpretación del posterior

### 9.2 Cuándo Bayes NO Aporta Nada

**Regla dura:**

> Si no te cuestionas el espacio taxa × loci, no uses Bayes por parecer moderno.

**Caso:** MSA fijo (100 taxa × 1000 loci), no se discute.

→ **MLE es suficiente.**

Optimizas la topología y parámetros. Punto.

Bayes no añade información. Solo añade tiempo de cómputo.

### 9.3 Cuándo Bayes SÍ Compensa

**Caso: Filogenia de epidemia viral en tiempo real**

Preguntas:
- ¿Incluyo las muestras de Cuenca?
- ¿Uso genoma completo o solo proteína de superficie?
- ¿Qué aporta añadir 50 secuencias más?
- ¿Es sólido el clado que propongo?

Estas preguntas **solo se responden integrando**, no maximizando.

**Por qué:**

- Cada nuevo taxón puede forzar ramas raras → penaliza evidencia marginal
- Cada locus conflictivo introduce inconsistencia → baja soporte posterior
- El soporte de un clado no es "likelihood alta en un árbol", es "masa del posterior concentrada en árboles que contienen ese clado"

### 9.4 La Pantallita de BEAST No Piensa Por Ti

**Modo casillas al tuntún (común, vacío):**

- Marcar "Bayes" porque está de moda
- Reloj estricto porque es default
- 10M iteraciones porque alguien lo puso en un tutorial
- ESS > 200 → "ya está"

**Esto no es inferencia. Es liturgia.**

**Modo pensar antes (único con sentido):**

- Cada casilla marcada es una hipótesis tácita
- El prior no es "un parámetro más", es la estructura que impones
- Las iteraciones no son un parámetro científico (es solo: ¿exploré bien el espacio que definí?)
- Convergencia ≠ corrección (solo significa: el algoritmo terminó de recorrer el espacio que tú le diste)

**Si el espacio es malo, convergir en él no te salva.**

---

## 10. CONCLUSIÓN: LA HONESTIDAD CIENTÍFICA

### 10.1 Los Modelos Son Conversaciones, No Fotografías

Un modelo probabilista no captura la realidad. 

Establece un **diálogo estructurado** entre:
- Lo que sabemos (prior)
- Lo que vemos (likelihood)
- Lo que podemos nombrar (espacio de estados)

### 10.2 Bayes Es Subjetivo (Y Eso Está Bien)

"Subjetivo" no significa "arbitrario sin control".

Significa:
- Dependiente de elecciones previas (espacio Ω, prior)
- Que deben ser **declaradas explícitamente**
- Y **justificadas** biológicamente, no estadísticamente

La subjetividad bayesiana es **honestidad**.

Los métodos que dicen "objetivos" solo **esconden** sus decisiones en defaults no documentados.

### 10.3 No Hay Garantías, Solo Capacidad de Corrección

No existe garantía de aproximación inicial razonable al espacio de datos.

Solo existe:
- Posibilidad de reajuste progresivo
- Capacidad de no romperse al crecer la historia

**No empezamos cerca de la verdad. Empezamos capaces de movernos sin perder coherencia.**

### 10.4 El Posterior De Hoy Es El Prior De Mañana

La inferencia no cierra el mundo.

Es un proceso que:
- Actualiza estado
- Mantiene memoria comprimida
- Permite seguir caminando

Cuando lleguen nuevos datos:

$$\text{Posterior}_{\text{hoy}} \rightarrow \text{Prior}_{\text{mañana}}$$

Y el ciclo continúa.

### 10.5 Lo Que Les Das A Los Alumnos

No les quitas certezas.

Les das:
- Lenguaje para nombrar lo que hacen
- Criterio para decidir antes de ejecutar
- Responsabilidad sin culpa
- Libertad para no esconderse detrás del software

**Un modelo no descubre la verdad. Descubre hasta dónde puede caminar sin romperse.**

Y eso —en ciencia— ya es muchísimo.

---

## EJERCICIOS

### Conceptuales

1. **Espacio de estados:** Estás modelando expresión génica de 1000 genes. Decides no incluir modificaciones epigenéticas. ¿Qué información puede (y qué no puede) inferir tu modelo? ¿Es eso una limitación técnica o conceptual?

2. **Prior vs. Likelihood:** En un modelo de evolución de secuencias, el prior favorece árboles balanceados (sin ramas muy largas). Los datos muestran una rama 10× más larga que el resto. ¿Qué debería dominar: prior o likelihood? ¿Depende de la cantidad de datos?

3. **AIC vs. BIC:** Tienes un MSA de 20 taxa × 500 loci. Comparas 3 modelos (JC, HKY, GTR). ¿Cuál esperarías que gane en AIC? ¿Y en BIC? ¿Por qué?

### Prácticos

4. **MLE simple:**
   - Simula 100 muestras de una distribución $N(\mu=5, \sigma=2)$
   - Calcula el MLE de μ (debería ser ≈ 5)
   - Repite con 10 muestras. ¿Qué pasa con la precisión?

5. **Comparación de modelos:**
   - Descarga un MSA pequeño (ej: 10 secuencias de citocromo b)
   - Usa PAUP*/RAxML para calcular likelihood bajo JC y HKY
   - Calcula AIC de ambos
   - ¿Cuál gana? ¿Por qué?

6. **Prior informativo:**
   - Estás infiriendo una tasa de mutación
   - Prior 1: Uniforme [0, ∞)
   - Prior 2: Lognormal centrada en valores conocidos de organismos similares
   - Simula datos con poca información (n=5)
   - Compara posteriors. ¿Cómo afecta el prior?

### Reflexivos

7. **Decisiones previas:** Describe un análisis bioinformático que hayas hecho (o planees hacer). Identifica explícitamente:
   - Tu espacio Ω (qué variables existen en tu modelo)
   - Tu prior (qué estructuras asumiste sin demostrarlo)
   - Tu likelihood (cómo mediste coherencia)

8. **Bayes en epidemia:** En una epidemia viral, ¿en qué momento del brote es más útil Bayes que MLE? ¿Por qué?

---

**FIN DEL CAPÍTULO 1**

*Siguiente: Capítulo 2 - Redes de Regulación Génica*

