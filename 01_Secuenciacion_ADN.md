# CAPÍTULO 1: SECUENCIACIÓN DE ADN
## El Genoma No Se Lee, Se Reconstruye

---

## INTRODUCCIÓN: EL PROBLEMA FUNDAMENTAL

### Un Libro Roto en Fragmentos

La secuenciación no es "leer el genoma". 

Un secuenciador no abre una célula y transcribe ordenadamente desde el cromosoma 1 hasta el 22 (o 23 si cuentas el sexual). 

Un secuenciador **destruye** el genoma. Lo rompe en millones de fragmentos. Lee esos fragmentos. Y luego te deja con el problema de reconstruir el libro entero desde las páginas arrancadas.

Y nadie te dice si falta alguna.

### La Metáfora del Accidente

Imagina un libro de 3.000 millones de letras. Sin espacios. Sin puntos. Sin capítulos.

Alguien lo mete en una trituradora industrial.

Obtienes:
- 100 millones de trozos
- De longitud variable (50-300 letras cada uno)
- Sin orden
- Con errores de copia
- Algunos duplicados
- Algunos perdidos

Tu trabajo: reconstruir el libro original.

**Eso es la secuenciación.**

Y el hecho de que funcione es casi un milagro matemático.

---

## 1. QUÉ ES SECUENCIAR (SIN MENTIRAS)

### 1.1 Definición Operativa

**Secuenciar es:**
Pasar de una muestra biológica (sangre, tejido, saliva) a un archivo digital que representa el orden de nucleótidos en el ADN de esas células.

**No es:**
- "Fotografiar" el ADN
- Leerlo directamente
- Obtener "la secuencia verdadera" sin errores

**Es:**
- Fragmentar el ADN
- Leer fragmentos cortos (reads)
- Reconstruir mediante algoritmos
- Obtener una **aproximación** al genoma original

### 1.2 Por Qué No Podemos "Leer Directamente"

El ADN es:
- Microscópico (2 nm de ancho)
- Largo (2 metros desenrollado en cada célula humana)
- Frágil (se degrada, se rompe)
- Químicamente uniforme (solo 4 bases: A, C, G, T)

No existe tecnología (todavía) que pueda:
- "Ver" las bases individuales en secuencia
- A lo largo de cromosomas completos
- Sin romper la molécula

Por eso todas las tecnologías actuales:
1. Rompen el ADN (fragmentación)
2. Leen fragmentos cortos (reads de 50-300 bp típicamente)
3. Reconstruyen informáticamente (ensamblaje)

### 1.3 La Paradoja Central

Para leer el genoma, primero tienes que destruirlo.

Eso define todo lo que viene después:
- El tipo de errores que cometes
- La información que pierdes
- Los algoritmos que necesitas
- Los límites de lo que puedes saber

---

## 2. HISTORIA: DE SANGER A NGS

### 2.1 Secuenciación de Sanger (1977-2005)

#### El Método Original

Sanger inventó un método químico brillante:
1. Toma ADN molde
2. Añade ddNTPs (nucleótidos "trampa" que detienen la síntesis)
3. Cada molécula se detiene en un punto aleatorio
4. Separas por tamaño en gel
5. Lees la secuencia de las bandas

**Ventajas:**
- Muy preciso (~99.9% de accuracy)
- Reads largos (500-1000 bp)
- Química estable y entendida

**Limitaciones brutales:**
- Lento (meses para un genoma bacteriano)
- Caro (millones de dólares por genoma humano)
- Bajo throughput (pocas secuencias simultáneas)

#### El Proyecto Genoma Humano (1990-2003)

El primer genoma humano:
- Costó **$3.000 millones**
- Duró **13 años**
- Involucró cientos de científicos en múltiples países
- Usó Sanger casi exclusivamente

**Ese era el estado del arte en 2003.**

Y aún así, el genoma quedó incompleto:
- Centrómeros sin resolver
- Regiones repetitivas ausentes
- Heterocigosidad mal resuelta

### 2.2 La Revolución: Next-Generation Sequencing (NGS)

#### Por Qué Cambió Todo

A mediados de los 2000s, varias tecnologías convergieron:
- Miniaturización (chips con millones de pocillos)
- Óptica avanzada (cámaras CCD de alta resolución)
- Química de secuenciación paralela
- Bioinformática para procesar datos masivos

**Resultado:**
En 2007, Illumina secuenció un genoma humano completo en **semanas** por menos de **$100,000**.

En 2023, un genoma humano cuesta **~$600** y se hace en **horas**.

#### Qué Cambió Conceptualmente

Antes (Sanger):
- Secuencias largas, pocas a la vez
- Precisión altísima
- Lento, caro

Después (NGS):
- Secuencias cortas, millones simultáneas
- Precisión menor por read (~99%)
- Rápido, barato
- La cobertura compensa los errores

**La idea clave:** 
Si lees el mismo sitio 30 veces (30× coverage), los errores aleatorios desaparecen por consenso.

### 2.3 Tecnologías Actuales (2023)

#### Illumina (Short-Read NGS)
- **Reads:** 50-300 bp
- **Output:** 50-3000 Gb por run
- **Precisión:** ~99.9% (después de corrección)
- **Aplicación:** Resequencing, RNA-seq, ChIP-seq
- **Limitación:** No resuelve bien repeticiones largas

#### Oxford Nanopore (Long-Read)
- **Reads:** 1 kb - 2 Mb (sí, megabases)
- **Output:** Variable
- **Precisión:** ~95-99% (mejorando)
- **Aplicación:** Ensamblajes de novo, regiones estructurales complejas
- **Ventaja:** Portátil (del tamaño de un USB)

#### PacBio HiFi (Long + Accurate)
- **Reads:** 10-25 kb
- **Output:** 30-90 Gb por run
- **Precisión:** >99.9% (lee cada molécula múltiples veces circularmente)
- **Aplicación:** Genomas de referencia de alta calidad
- **Costo:** Más caro que Illumina

---

## 3. CÓMO FUNCIONA ILLUMINA (DETALLE TÉCNICO)

### 3.1 Overview del Proceso

1. **Preparación de librería:** Fragmentar ADN, añadir adaptadores
2. **Cluster generation:** Amplificar cada fragmento en un spot del chip
3. **Secuenciación por síntesis:** Leer bases una a una con fluorescencia
4. **Base calling:** Convertir intensidades de luz en letras (A,C,G,T)
5. **Output:** Archivo FASTQ con millones de reads

### 3.2 Paso 1: Preparación de Librería

#### Fragmentación
- ADN genómico (largo) → fragmentos de ~300-500 bp
- Métodos: sonicación, enzimático (transposasa)

#### Adaptadores
- Se ligan secuencias sintéticas en ambos extremos de cada fragmento
- Los adaptadores permiten:
  - Unirse al flow cell
  - Amplificación
  - Secuenciación desde extremos definidos

```
Original: [ADN genómico largo]
Fragmentado: [fragmento 1] [fragmento 2] [fragmento 3] ...
Con adaptadores: [P5]-[fragmento 1]-[P7]
                 [P5]-[fragmento 2]-[P7]
```

Donde P5 y P7 son secuencias adaptadoras conocidas.

### 3.3 Paso 2: Cluster Generation

El flow cell de Illumina es una superficie con millones de **oligos** (primers cortos) covalentemente unidos.

**Proceso:**
1. Fragmentos con adaptadores se unen al flow cell (mediante hibridación P5/P7)
2. **Bridge amplification:**
   - El fragmento se dobla y se une al oligo vecino
   - Se amplifica mediante PCR in situ
   - Cada ciclo duplica localmente
   - Resultado: ~1000 copias idénticas en un "cluster" de ~1 µm

**Por qué es necesario:**
- Señal fluorescente de una molécula individual es demasiado débil
- El cluster amplifica la señal 1000×
- Permite detectar con cámaras CCD

### 3.4 Paso 3: Sequencing by Synthesis (SBS)

#### Química del Proceso

En cada ciclo:
1. Se añaden los 4 dNTPs (A, C, G, T) marcados con fluoróforos distintos
2. **Y bloqueados químicamente:** cada nucleótido incorporado detiene la síntesis
3. Polimerasa incorpora el correcto según complementariedad
4. Se lava (elimina nucleótidos no incorporados)
5. Se ilumina con láser
6. Cámara captura la fluorescencia de cada cluster
7. Se elimina el fluoróforo y el bloqueo químico
8. **Repite** para la siguiente base

**Esquema de un ciclo:**

```
Ciclo N:
  Cluster con cadena sintetizada hasta posición n
  + 4 dNTPs fluorescentes bloqueados
  → Polimerasa incorpora el complementario
  → Lavado → Imagen → Desbloqueo

Ciclo N+1:
  Ahora la cadena tiene n+1 bases
  Repite
```

#### Por Qué Es Brillante

- Lee **todas las moléculas en paralelo** (millones de clusters simultáneamente)
- Cada ciclo añade solo **1 base**, así que la precisión es alta
- La fluorescencia diferencial (4 colores) permite distinguir A, C, G, T

### 3.5 Paso 4: Base Calling

#### De Imagen a Secuencia

Cada ciclo produce una imagen del flow cell (millones de clusters con puntos de luz).

**El software de base calling (p.ej., RTA - Real-Time Analysis de Illumina) hace:**
1. Identificar cada cluster (posición x,y en el chip)
2. Medir intensidad de fluorescencia en los 4 canales (A,C,G,T)
3. Asignar la base más probable según intensidades
4. Calcular **quality score** (Phred score)

#### Quality Scores (Phred)

Para cada base leída, se asigna una confianza:

$$Q = -10 \log_{10}(P_{\text{error}})$$

Ejemplos:
- Q20 → P(error) = 1/100 → 99% correcto
- Q30 → P(error) = 1/1000 → 99.9% correcto
- Q40 → P(error) = 1/10000 → 99.99% correcto

**Por qué importa:**
La calidad **decrece** a lo largo del read:
- Primeras 50 bases: Q30-Q40
- Bases 100-150: Q25-Q30
- Últimas bases (>200): Q15-Q25

Esto es por acumulación de errores:
- Phasing (algunas moléculas van adelantadas/atrasadas)
- Fluoróforos residuales
- Degradación química

### 3.6 Paso 5: Output - Archivo FASTQ

Cada read se representa como 4 líneas:

```
@Read_ID información adicional
ACGTACGTACGTACGT... (la secuencia)
+
IIIIIHHHHGGGFFFEE... (quality scores en ASCII)
```

**Ejemplo real:**

```
@HWI-ST0001:123:ABCD1234:1:1101:1234:5678 1:N:0:ATCACG
NTTGCATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGATCGAT
+
#8BCCGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGGG
```

Línea 3 (quality): Caracteres ASCII donde:
- `I` = Q40
- `G` = Q38
- `#` = Q2 (muy bajo, posición con N)

---

## 4. DE READS A GENOMA: EL PROBLEMA DEL ENSAMBLAJE

### 4.1 El Reto Central

Tienes:
- 100 millones de reads
- De 150 bp cada uno
- Sin saber de dónde vienen en el genoma
- Con errores (~1% por read)
- Algunos reads duplicados (PCR bias)
- Algunas regiones no cubiertas

Quieres:
- Reconstruir el genoma completo
- De 3.000 millones de bp
- En el orden correcto
- Sin ambigüedades

**Esto es computacionalmente difícil.**

No es "difícil" como "lleva tiempo".
Es "difícil" como "matemáticamente intratable sin heurísticas inteligentes".

### 4.2 Dos Estrategias: Mapping vs. Assembly

#### Estrategia 1: Mapping (Resequencing)

**Cuándo:** Ya tienes un genoma de referencia (p.ej., humano GRCh38)

**Qué haces:**
1. Tomas cada read
2. Buscas en el genoma de referencia dónde encaja mejor
3. Alineas todos los reads a la referencia
4. Identificas diferencias (SNPs, indels, SVs)

**Herramientas:** BWA, Bowtie2, STAR (para RNA-seq)

**Ventajas:**
- Rápido (horas en un servidor)
- No requiere ensamblaje de novo
- Funciona bien con reads cortos

**Limitaciones:**
- Sesgado hacia la referencia
- No detecta bien regiones estructuralmente distintas
- No funciona si tu organismo no tiene referencia

#### Estrategia 2: De Novo Assembly

**Cuándo:** No tienes referencia (nuevo organismo, genoma tumoral muy alterado)

**Qué haces:**
1. Buscar solapamientos (overlaps) entre todos los reads
2. Construir un grafo donde:
   - Nodos = reads
   - Aristas = solapamientos
3. Encontrar caminos en el grafo que reconstruyan el genoma
4. Resolver ambigüedades (repeats, errores)

**Herramientas:** SPAdes, Velvet, MEGAHIT

**Ventajas:**
- No depende de referencia
- Detecta todo (incluso variación estructural novedosa)

**Limitaciones:**
- Computacionalmente intensivo (días/semanas)
- Produce contigs (fragmentos) no cromosomas completos
- Sensible a errores y cobertura

### 4.3 Por Qué Los Repeats Son El Infierno

#### El Problema de las Regiones Repetitivas

El genoma humano contiene ~50% de secuencias repetitivas:
- SINEs (Short Interspersed Nuclear Elements): Alu, ~1 millón de copias
- LINEs (Long ...): LINE-1, ~500,000 copias
- Microsatélites: (CA)n, (GAA)n
- Duplicaciones segmentales: regiones de varios kb casi idénticas

**Por qué es un problema:**

Si tienes un read de 150 bp que cae en un Alu:
- Hay ~1 millón de sitios posibles donde podría mapear
- Todos casi idénticos
- No puedes saber cuál es el correcto

En ensamblaje de novo:
- Repeats crean **burbujas** en el grafo
- Múltiples caminos posibles
- El ensamblador no puede resolverlo
- Resultado: genoma fracturado en contigs

#### La Solución Parcial: Paired-End Sequencing

En lugar de leer solo un extremo del fragmento:
1. Secuencias **ambos extremos** del mismo fragmento (~300-500 bp)
2. Sabes que ambos reads están separados por ~300 bp
3. Eso crea **restricción de distancia**

**Ejemplo:**

```
Fragmento: [===========300 bp===========]
           |<--read1   espacio   read2-->|

Si read1 mapea en posición X, read2 debe mapear en X+300
```

Eso ayuda a resolver algunas ambigüedades.

Pero **no** resuelve repeats más largos que el tamaño del fragmento.

#### Por Qué Long-Reads Cambian el Juego

Nanopore / PacBio pueden leer 10-100 kb.

Si un repeat mide 5 kb:
- Short-read: cae dentro, ambiguo
- Long-read: atraviesa el repeat completo, ancla en ambos lados únicos

**Eso resuelve el ensamblaje.**

Por eso los genomas de referencia modernos se ensamblan con long-reads.

---

## 5. CONCEPTOS CLAVE: COBERTURA, PROFUNDIDAD, CALIDAD

### 5.1 Cobertura (Coverage)

**Definición:**
Cuántas veces, en promedio, cada base del genoma es leída.

$$\text{Coverage} = \frac{\text{Total bases secuenciadas}}{\text{Tamaño del genoma}}$$

**Ejemplo:**
- Genoma humano: 3 Gb
- Secuencias 90 Gb de reads
- Coverage = 90/3 = **30×**

#### Por Qué Importa

**Baja cobertura (5-10×):**
- Muchas regiones sin cubrir
- No puedes distinguir errores de variantes reales
- Genoma incompleto

**Alta cobertura (30-50×):**
- Casi todo el genoma cubierto
- Errores se promedian
- Variantes heterocigotas detectables (aparecer en ~50% de reads)

**Cobertura no uniforme:**
En la práctica, algunas regiones tienen 50× y otras 5×:
- Bias de GC (regiones ricas en GC se amplifican mal)
- Regiones estructurales complejas
- Artefactos de preparación de librería

### 5.2 Profundidad (Depth) en Una Posición Específica

Si la cobertura es el promedio, la profundidad es el valor local.

**Ejemplo:**
```
Posición:  100   101   102   103   104
Depth:     28    32    30    15    29
```

Posición 103 tiene solo 15 reads (baja profundidad local), aunque el promedio sea 30×.

### 5.3 Calidad del Mapping

Cuando mapeas un read a la referencia, el aligner (p.ej., BWA) asigna:
- **MAPQ (Mapping Quality):** Confianza de que el read está en el sitio correcto

$$\text{MAPQ} = -10 \log_{10}(P_{\text{mapping incorrecto}})$$

- MAPQ = 60 → P(error) = 1/1,000,000 (único mapeo)
- MAPQ = 30 → P(error) = 1/1000
- MAPQ = 0 → Ambiguo (múltiples sitios posibles)

**Filtrado típico:**
Descartar reads con MAPQ < 20 (demasiado ambiguos).

---

## 6. FORMATOS DE ARCHIVO ESTÁNDAR

### 6.1 FASTQ (Raw Reads)

Ya visto. Contiene:
- Secuencia + calidad por base
- Archivo de entrada para todo análisis

**Tamaño típico:**
- Genoma humano 30×: ~300 millones de reads
- Archivo FASTQ comprimido: ~50-80 GB

### 6.2 SAM/BAM (Aligned Reads)

Después de mapear, los reads se guardan en **SAM** (Sequence Alignment/Map):
- Formato de texto
- Cada línea = un read alineado
- Incluye: posición, CIGAR string (cómo alinea), calidad

**BAM** es la versión binaria comprimida (más pequeño, más rápido).

**Campos esenciales:**
```
QNAME FLAG RNAME POS MAPQ CIGAR SEQ QUAL
```

**Ejemplo:**
```
read1 0 chr1 1000 60 100M ACGT... IIII...
```

- Posición 1000 del chr1
- MAPQ=60 (alta calidad)
- CIGAR=100M (100 bases matched)

### 6.3 VCF (Variant Call Format)

Después de detectar variantes (SNPs, indels), se guardan en **VCF**:

```
#CHROM POS ID REF ALT QUAL FILTER INFO
chr1   1000 . A   G   999  PASS   DP=30
```

- Posición 1000: referencia es A, muestra tiene G
- Profundidad = 30 reads
- Calidad = 999 (muy confiable)

---

## 7. LIMITACIONES Y ARTEFACTOS

### 7.1 Sesgos de GC

Regiones ricas en GC (>60%) o pobres (<30%) se amplifican mal en PCR:
- Baja cobertura
- Posibles false negatives (variantes no detectadas)

### 7.2 Errores de Secuenciación

Aunque la precisión por base es ~99.9%, con 3 Gb:
- Se esperan ~3 millones de errores
- La cobertura múltiple permite corregirlos por consenso
- Pero en regiones de baja cobertura, pueden parecer variantes reales

### 7.3 Duplicados de PCR

Durante la amplificación, algunos fragmentos se amplifican más que otros:
- Múltiples reads idénticos del mismo fragmento original
- Inflan artificialmente la cobertura
- Herramientas como **Picard MarkDuplicates** los identifican y marcan

### 7.4 Regiones No Resolubles (Con Short-Reads)

- Centrómeros (repeticiones masivas)
- Telómeros
- Duplicaciones segmentales recientes
- Algunos genes (HLA, inmunoglobulinas)

**Solución:** Long-reads (Nanopore, PacBio).

---

## 8. APLICACIONES: QUÉ SE SECUENCIA Y POR QUÉ

### 8.1 Whole Genome Sequencing (WGS)

**Qué:** Todo el genoma
**Cobertura típica:** 30-50×
**Uso:** Diagnóstico clínico, estudios poblacionales
**Costo (~2023):** $600-1000 por genoma humano

### 8.2 Whole Exome Sequencing (WES)

**Qué:** Solo regiones codificantes (~1-2% del genoma)
**Cobertura típica:** 100-150×
**Uso:** Enfermedades genéticas raras (enriquecimiento en regiones funcionales)
**Costo:** $300-500
**Limitación:** No detecta variantes en regiones regulatorias

### 8.3 RNA-seq (Transcriptómica)

**Qué:** ARN mensajero (transcriptoma)
**Proceso:** 
1. Extraer ARN
2. Convertir a cDNA (retrotranscripción)
3. Secuenciar como DNA

**Uso:**
- Expresión génica diferencial
- Splice variants
- Fusiones génicas (en cáncer)

**Limitación:** No te dice variantes genómicas, solo qué se expresa.

### 8.4 ChIP-seq, ATAC-seq (Epigenómica)

**ChIP-seq:**
- Inmunoprecipita ADN unido a proteína (p.ej., histona modificada)
- Secuencia esos fragmentos
- Identifica regiones regulatorias activas

**ATAC-seq:**
- Identifica cromatina accesible (abierta)
- Marca regiones regulatorias activas

**Uso:** Mapear enhancers, promotores, sitios de unión de TFs.

---

## 9. PIPELINE TÍPICO: DE MUESTRA A VARIANTES

### 9.1 Flujo Completo

```
[Muestra biológica]
    ↓ Extracción de ADN
[ADN genómico]
    ↓ Preparación de librería
[Librería lista para secuenciar]
    ↓ Secuenciación (Illumina)
[Archivo FASTQ] (raw reads)
    ↓ Control de calidad (FastQC)
[FASTQ limpio]
    ↓ Mapping a referencia (BWA)
[Archivo BAM] (reads alineados)
    ↓ Marcar duplicados (Picard)
    ↓ Recalibrar quality scores (GATK)
[BAM refinado]
    ↓ Variant calling (GATK HaplotypeCaller)
[Archivo VCF] (variantes detectadas)
    ↓ Filtrado (calidad, frecuencia poblacional)
[VCF final]
    ↓ Anotación (¿qué gen afecta? ¿es patogénica?)
[Variantes anotadas]
    ↓ Interpretación clínica / biológica
[Resultado]
```

### 9.2 Herramientas Esenciales

- **FastQC:** Control de calidad de FASTQ
- **BWA / Bowtie2:** Mapping
- **SAMtools:** Manipular archivos BAM
- **Picard:** Marcar duplicados
- **GATK:** Variant calling (estándar en clínica)
- **VEP / SnpEff:** Anotación de variantes

---

## 10. CONCLUSIÓN: LA SECUENCIACIÓN COMO FUNDAMENTO

### La Secuencia No Es La Respuesta

Un archivo FASTQ o un VCF no te dicen nada por sí mismos.

Son **datos crudos**. 

La biología empieza cuando:
- Comparas (siguiente bloque: BLAST, MSA)
- Interpretas evolutivamente (bloque 4)
- Integras en redes regulatorias (bloque 5)

### Por Qué Importa Entender el Proceso

Muchos investigadores tratan la secuenciación como una "caja negra":
- Envían muestra
- Reciben VCF
- Nunca se preguntan qué pasó en medio

**Eso es peligroso.**

Porque:
- No sabrás identificar artefactos
- No entenderás por qué falta información en ciertas regiones
- No podrás distinguir error técnico de biología real

### La Secuenciación Es Reconstrucción, No Lectura

Cada genoma secuenciado es una **hipótesis reconstruida** a partir de fragmentos.

No es "la verdad".

Es la mejor aproximación dado:
- La tecnología usada
- La cobertura alcanzada
- Los algoritmos empleados
- Las regiones del genoma accesibles

Y esa diferencia —entre medir y reconstruir— es lo que hace la bioinformática necesaria.

---

## EJERCICIOS

### Conceptuales

1. **Paradoja de la secuenciación:** Explica por qué, para leer el genoma, primero tienes que destruirlo. ¿Qué implicaciones tiene esto para la completitud del resultado?

2. **Cobertura vs. Profundidad:** Un genoma tiene cobertura promedio 30×. ¿Significa eso que todas las bases tienen profundidad 30? ¿Qué regiones esperarías que tengan profundidad más baja y por qué?

3. **Short-reads vs. Long-reads:** ¿Por qué los repeats de 10 kb son irresolvibles con Illumina (reads de 150 bp) pero sí con Nanopore (reads de 20 kb)? Dibuja un esquema.

### Prácticos

4. **Análisis de un FASTQ:**
   - Descarga un archivo FASTQ pequeño (p.ej., de E. coli)
   - Usa FastQC para evaluar calidad
   - Identifica: ¿hay caída de calidad al final de los reads? ¿Hay sobrerepresentación de alguna secuencia?

5. **Mapping básico:**
   - Toma 10,000 reads de un genoma humano
   - Mapéalos a GRCh38 con BWA
   - Visualiza en IGV (Integrative Genomics Viewer)
   - Pregunta: ¿Todos los reads mapearon? ¿Cuántos tienen MAPQ < 20?

6. **Simulación de cobertura:**
   - Simula un genoma de 1 Mb
   - Genera reads al azar (150 bp cada uno)
   - Calcula la cobertura necesaria para que el 95% del genoma tenga profundidad ≥ 10

---

**FIN DEL CAPÍTULO 1**

*Siguiente: Capítulo 2 - Comparación de Secuencias (BLAST, Dotplot, MSA)*
