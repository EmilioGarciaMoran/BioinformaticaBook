# CAPÍTULO 0A: UNIX Y LA TERMINAL
## El Lenguaje Que Las Máquinas Entienden Cuando Les Pides Hacer Ciencia

---

## INTRODUCCIÓN: POR QUÉ UNIX

### No Es Un Sistema Operativo Moderno

Unix tiene más de 50 años. Nació en 1969 en los laboratorios Bell.

No tiene:
- Interfaz gráfica elegante
- Menús desplegables intuitivos
- Íconos que arrastrar
- Nada de lo que un usuario "normal" esperaría

Y sin embargo.

Todo servidor de secuenciación usa Unix.
Todo cluster de supercomputación usa Unix.
Todo pipeline de bioinformática se escribe en Unix.

¿Por qué?

### La Filosofía Unix: Hacer Una Cosa Y Hacerla Bien

Los creadores de Unix (Thompson, Ritchie) entendieron algo fundamental:

**La complejidad no se domina con interfaces sofisticadas. Se domina combinando simplicidades.**

Un comando hace una cosa.
Si necesitas dos cosas, usas dos comandos.
Si necesitas que uno alimente al otro, los encadenas.

Eso es todo.

Y con eso, puedes procesar genomas enteros desde tu terminal.

### Por Qué No Puedes Escapar de la Terminal

La bioinformática produce datos masivos:
- Archivos de 50 GB (FASTQ de un genoma humano)
- Millones de secuencias
- Operaciones repetitivas sobre miles de archivos

**Ninguna interfaz gráfica puede manejar eso.**

No es que "sea más difícil". Es que es el único método escalable.

Cuando procesas 100 muestras simultáneamente, no puedes:
- Abrir 100 ventanas
- Hacer clic 100 veces
- Esperar que Excel cargue 100 millones de filas

Pero sí puedes escribir:

```bash
for file in *.fastq; do process_sample $file; done
```

Y ya está. 100 muestras procesadas mientras duermes.

---

## 1. ANATOMÍA DE LA TERMINAL

### 1.1 Qué Es La Terminal

La terminal (o shell) es un intérprete de comandos.

Tú escribes texto.
La máquina ejecuta.
Te devuelve resultado (o error).

**No hay más.**

### 1.2 El Prompt

Cuando abres una terminal, ves algo como:

```
usuario@maquina:~$
```

Eso es el **prompt**. Te dice:
- **usuario:** Quién eres
- **maquina:** En qué servidor estás
- **~:** Dónde estás (tu home directory)
- **$:** Esperando tu comando (si fuera root/admin, sería #)

### 1.3 Tu Primer Comando

```bash
pwd
```

**P**rint **W**orking **D**irectory

Te dice dónde estás en el sistema de archivos.

```
/home/usuario
```

Eso es tu "ubicación actual".

---

## 2. NAVEGACIÓN: MOVERSE POR EL SISTEMA

### 2.1 El Sistema de Archivos Es Un Árbol

Unix organiza todo como un árbol jerárquico:

```
/                     (raíz del sistema)
├── home/             (directorios de usuarios)
│   ├── usuario1/
│   └── usuario2/
├── usr/              (programas instalados)
├── var/              (datos variables, logs)
├── tmp/              (archivos temporales)
└── mnt/              (puntos de montaje)
```

**Todo** es un archivo:
- Archivos normales
- Directorios (carpetas)
- Dispositivos (discos, impresoras)
- Procesos

### 2.2 Comandos Básicos de Navegación

#### `ls` - Listar contenido

```bash
ls
```

Muestra archivos y carpetas en el directorio actual.

```bash
ls -l
```

Formato largo (con permisos, tamaño, fecha):

```
-rw-r--r-- 1 user group 1024 Feb 9 10:00 file.txt
drwxr-xr-x 2 user group 4096 Feb 9 10:01 data/
```

- Primera columna: permisos
- `d` = directorio, `-` = archivo
- `rw-` (read/write para el dueño), `r--` (solo read para otros)

```bash
ls -lh
```

Con tamaños legibles (K, M, G en lugar de bytes).

#### `cd` - Cambiar directorio

```bash
cd data/
```

Entra en la carpeta `data/`.

```bash
cd ..
```

Sube un nivel (al directorio padre).

```bash
cd ~
```

Vuelve a tu home.

```bash
cd /usr/local/bin
```

Ruta absoluta (desde la raíz `/`).

#### `mkdir` - Crear directorio

```bash
mkdir proyecto_genoma
```

Crea una carpeta.

```bash
mkdir -p proyecto/data/raw
```

Crea jerarquía completa (padres incluidos si no existen).

---

## 3. MANIPULACIÓN DE ARCHIVOS

### 3.1 Ver Contenido: `cat`, `less`, `head`, `tail`

#### `cat` - Concatenar y mostrar

```bash
cat archivo.txt
```

Imprime todo el contenido en pantalla.

**Problema:** Si el archivo tiene 1 millón de líneas, inunda la terminal.

#### `less` - Ver con paginación

```bash
less archivo.txt
```

Abre un visor:
- Espacio: siguiente página
- b: página anterior
- /patrón: buscar
- q: salir

**Esto es lo que usas** para archivos grandes.

#### `head` - Primeras líneas

```bash
head -n 10 secuencias.fasta
```

Muestra las primeras 10 líneas.

**Uso típico:** Ver el formato de un archivo sin abrirlo todo.

#### `tail` - Últimas líneas

```bash
tail -n 20 log_pipeline.txt
```

Muestra las últimas 20 líneas.

**Uso crítico:** Monitorear logs de procesos corriendo.

```bash
tail -f log_pipeline.txt
```

Modo "follow": actualiza en tiempo real (como ver un log "en vivo").

### 3.2 Copiar, Mover, Borrar: `cp`, `mv`, `rm`

#### `cp` - Copiar

```bash
cp archivo.txt copia.txt
```

Copia archivo.

```bash
cp -r directorio/ copia_directorio/
```

Copia recursivamente (todo el contenido).

#### `mv` - Mover (o renombrar)

```bash
mv viejo_nombre.txt nuevo_nombre.txt
```

Renombra.

```bash
mv archivo.txt /otra/ubicacion/
```

Mueve a otro directorio.

#### `rm` - Borrar (¡IRREVERSIBLE!)

```bash
rm archivo.txt
```

Borra archivo.

```bash
rm -r directorio/
```

Borra directorio y todo su contenido.

**CUIDADO EXTREMO:**

```bash
rm -rf /
```

**NO EJECUTES ESO NUNCA.** Borra todo el sistema.

Unix no pregunta "¿estás seguro?". Asume que sabes lo que haces.

### 3.3 Buscar Archivos: `find`

```bash
find . -name "*.fastq"
```

Busca todos los archivos `.fastq` desde el directorio actual (`.`) recursivamente.

```bash
find /data -type f -size +1G
```

Busca archivos (`-type f`) mayores de 1 GB.

**Uso típico:** Localizar archivos en directorios complejos de proyectos bioinformáticos.

---

## 4. PROCESAMIENTO DE TEXTO: EL CORAZÓN DE UNIX

### 4.1 Por Qué El Texto Es Rey

En Unix, casi todo es texto:
- Archivos de configuración
- Logs
- Datos biológicos (FASTA, FASTQ, VCF, GFF)

Saber manipular texto es saber hacer bioinformática.

### 4.2 `grep` - Buscar Patrones

```bash
grep "ATCG" secuencias.fasta
```

Busca líneas que contengan "ATCG".

```bash
grep -c "^>" secuencias.fasta
```

Cuenta (`-c`) líneas que empiezan (`^`) con `>` → número de secuencias en un FASTA.

```bash
grep -v "^#" archivo.vcf
```

Muestra líneas que NO (`-v`) empiecen con `#` → omite headers del VCF.

**Uso crítico:** Filtrar datos masivos sin cargar en memoria.

### 4.3 `wc` - Contar Líneas/Palabras/Caracteres

```bash
wc -l archivo.txt
```

Cuenta líneas.

**Uso típico:**

```bash
grep "^>" secuencias.fasta | wc -l
```

Cuenta secuencias en un FASTA.

### 4.4 `cut` - Extraer Columnas

Archivos tabulares (separados por tabuladores o comas) son omnipresentes.

```bash
cut -f 1,3 archivo.tsv
```

Extrae columnas 1 y 3 (separadas por tabulador).

```bash
cut -d ',' -f 2 archivo.csv
```

Columna 2 de un CSV (separador `,`).

**Ejemplo biológico:**

```
# archivo: muestras.txt
ID001  sample1  tumor   30
ID002  sample2  normal  28
ID003  sample3  tumor   32
```

```bash
cut -f 1,3 muestras.txt
```

Output:

```
ID001  tumor
ID002  normal
ID003  tumor
```

### 4.5 `sort` - Ordenar

```bash
sort -k 3 -n datos.txt
```

Ordena por columna 3 (`-k 3`), numérico (`-n`).

```bash
sort -u lista.txt
```

Ordena y elimina duplicados (`-u` = unique).

### 4.6 `uniq` - Eliminar Duplicados Consecutivos

**Importante:** Solo elimina duplicados **consecutivos**. Por eso casi siempre se usa después de `sort`.

```bash
sort lista.txt | uniq
```

### 4.7 `awk` - El Procesador de Texto Más Poderoso

`awk` es un lenguaje de programación completo para procesar texto estructurado.

#### Sintaxis básica:

```bash
awk '{comando}' archivo
```

Ejecuta `comando` en cada línea.

#### Ejemplos:

**Imprimir columnas:**

```bash
awk '{print $1, $3}' datos.txt
```

Columnas 1 y 3 (separadas por espacios por defecto).

**Filtrar por condición:**

```bash
awk '$3 > 50' datos.txt
```

Muestra líneas donde columna 3 > 50.

**Calcular:**

```bash
awk '{sum += $2} END {print sum}' valores.txt
```

Suma todos los valores de columna 2 e imprime el total.

**Ejemplo biológico (FASTQ):**

```bash
awk 'NR%4==2' archivo.fastq
```

Imprime solo línea 2 de cada grupo de 4 (la secuencia).

### 4.8 `sed` - Editor de Streams

`sed` edita texto "al vuelo" sin abrir el archivo.

**Reemplazar texto:**

```bash
sed 's/viejo/nuevo/' archivo.txt
```

Reemplaza "viejo" por "nuevo" (primera ocurrencia por línea).

```bash
sed 's/viejo/nuevo/g' archivo.txt
```

Global (`g`): todas las ocurrencias.

**Borrar líneas:**

```bash
sed '/^#/d' archivo.vcf
```

Borra líneas que empiezan con `#`.

**Caso de uso:** Limpiar headers de archivos de datos.

---

## 5. PIPES Y REDIRECCIÓN: EL PODER REAL

### 5.1 Pipe (`|`) - Encadenar Comandos

El pipe toma la salida de un comando y la pasa como entrada al siguiente.

```bash
cat archivo.txt | grep "patrón" | wc -l
```

1. `cat` imprime archivo
2. `grep` filtra líneas con "patrón"
3. `wc -l` cuenta cuántas líneas quedaron

**Sin pipes, necesitarías archivos temporales:**

```bash
cat archivo.txt > tmp1
grep "patrón" tmp1 > tmp2
wc -l tmp2
rm tmp1 tmp2
```

**Con pipes:**

Una línea. Sin archivos intermedios. Más rápido. Más claro.

### 5.2 Redirección de Salida

#### `>` - Sobreescribir

```bash
ls -l > lista_archivos.txt
```

Guarda el output de `ls` en archivo (lo crea o sobreescribe).

#### `>>` - Añadir

```bash
echo "Nueva línea" >> log.txt
```

Añade al final sin borrar lo anterior.

#### `2>` - Redirigir Errores

```bash
comando 2> errores.log
```

Los mensajes de error van a `errores.log`, el output normal a pantalla.

```bash
comando > output.txt 2> errores.log
```

Output normal a `output.txt`, errores a `errores.log`.

```bash
comando > todo.log 2>&1
```

Todo (output + errores) al mismo archivo.

### 5.3 Redirección de Entrada (`<`)

```bash
programa < input.txt
```

Pasa `input.txt` como entrada a `programa`.

---

## 6. LOOPS Y SCRIPTING: AUTOMATIZAR TODO

### 6.1 El Loop `for`

```bash
for archivo in *.fastq; do
    echo "Procesando $archivo"
    fastqc $archivo
done
```

Ejecuta `fastqc` en cada archivo `.fastq`.

**Desglose:**
- `for archivo in *.fastq` → itera sobre archivos
- `do ... done` → bloque de comandos a ejecutar
- `$archivo` → valor de la variable en cada iteración

### 6.2 Ejemplo Biológico Real

Tienes 50 muestras con nombres:

```
sample_01.fastq
sample_02.fastq
...
sample_50.fastq
```

Quieres alinearlas todas a una referencia:

```bash
for sample in sample_*.fastq; do
    bwa mem reference.fa $sample > ${sample%.fastq}.sam
done
```

**Explicación:**
- `${sample%.fastq}` → quita extensión `.fastq` del nombre
- `> ${sample%.fastq}.sam` → output a `sample_01.sam`, etc.

**Resultado:** 50 alineamientos mientras tomas café.

### 6.3 Condicionales: `if`

```bash
if [ -f archivo.txt ]; then
    echo "El archivo existe"
else
    echo "No existe"
fi
```

**Uso típico:** Validar que archivos de entrada existen antes de procesarlos.

### 6.4 Scripts de Bash

Todo lo anterior puedes ponerlo en un archivo de texto (script):

**archivo: `procesar_muestras.sh`**

```bash
#!/bin/bash
# Script para procesar muestras de RNA-seq

REFERENCE="genome.fa"

for sample in data/*.fastq; do
    echo "Procesando $sample..."
    fastqc $sample
    bwa mem $REFERENCE $sample > ${sample%.fastq}.sam
done

echo "Proceso completado"
```

**Ejecutar:**

```bash
bash procesar_muestras.sh
```

O darle permisos y ejecutar directamente:

```bash
chmod +x procesar_muestras.sh
./procesar_muestras.sh
```

---

## 7. PERMISOS: QUIÉN PUEDE HACER QUÉ

### 7.1 El Sistema de Permisos Unix

Cada archivo tiene:
- **Owner (dueño):** El usuario que lo creó
- **Group (grupo):** Un grupo de usuarios
- **Others (otros):** El resto del mundo

Para cada categoría, hay 3 permisos:
- **r (read):** Leer
- **w (write):** Escribir
- **x (execute):** Ejecutar

### 7.2 Leer Permisos: `ls -l`

```
-rwxr-xr-- 1 user group 1024 Feb 9 10:00 script.sh
```

Desglose:
- `-` → archivo normal (no directorio)
- `rwx` → Owner puede read/write/execute
- `r-x` → Group puede read/execute (no write)
- `r--` → Others solo read

### 7.3 Cambiar Permisos: `chmod`

```bash
chmod +x script.sh
```

Añade permiso de ejecución.

```bash
chmod 755 script.sh
```

Notación octal:
- 7 = rwx (owner)
- 5 = r-x (group)
- 5 = r-x (others)

**Regla práctica:**
- Scripts: `chmod +x` (hacerlos ejecutables)
- Datos: dejar por defecto (no necesitan ejecutarse)

---

## 8. PROCESOS: QUÉ ESTÁ CORRIENDO

### 8.1 Ver Procesos: `ps`, `top`

```bash
ps
```

Muestra procesos del usuario actual.

```bash
ps aux
```

Todos los procesos del sistema.

```bash
top
```

Monitor interactivo (actualización en tiempo real).

**Uso crítico:** Ver si tu alineamiento sigue corriendo o se colgó.

### 8.2 Matar Procesos: `kill`

```bash
ps aux | grep bwa
```

Encuentra el PID (Process ID) del proceso.

```bash
kill 12345
```

Termina proceso con PID 12345.

```bash
kill -9 12345
```

"Mata inmediatamente" (forzado). Último recurso si no responde.

### 8.3 Correr en Background: `&`, `nohup`

```bash
bwa mem genome.fa sample.fastq > output.sam &
```

El `&` al final corre el comando en background (no bloquea la terminal).

```bash
nohup comando &
```

Corre en background **y** sigue corriendo si cierras la terminal.

**Output:**

```bash
nohup comando > output.log 2>&1 &
```

Todo va a `output.log`, corre en background, sobrevive si cierras sesión.

---

## 9. VARIABLES DE ENTORNO

### 9.1 Qué Son

Variables globales que definen el comportamiento del sistema.

```bash
echo $PATH
```

Muestra la variable `PATH`: directorios donde el sistema busca comandos.

```
/usr/local/bin:/usr/bin:/bin
```

### 9.2 Añadir a PATH

Tienes un programa instalado en `/home/usuario/software/bin`.

Para ejecutarlo desde cualquier lugar:

```bash
export PATH="$PATH:/home/usuario/software/bin"
```

**Hacerlo permanente:** Añadir esa línea a `~/.bashrc` (se ejecuta al abrir terminal).

---

## 10. COMPRESIÓN: ARCHIVOS GRANDES

### 10.1 Formatos Comunes

- `.gz` → gzip (más común)
- `.bz2` → bzip2 (más compresión, más lento)
- `.tar` → Tar (empaqueta múltiples archivos sin comprimir)
- `.tar.gz` → Tar + gzip

### 10.2 Comprimir/Descomprimir

```bash
gzip archivo.fastq
```

Crea `archivo.fastq.gz` (y borra el original).

```bash
gunzip archivo.fastq.gz
```

Descomprime.

```bash
tar -czf proyecto.tar.gz proyecto/
```

Empaqueta y comprime directorio completo.

```bash
tar -xzf proyecto.tar.gz
```

Desempaqueta.

**Importante:** Muchos programas bioinformáticos leen `.gz` directamente (no necesitas descomprimir).

---

## 11. CONEXIÓN REMOTA: `ssh` y `scp`

### 11.1 SSH - Conectar a Servidor

```bash
ssh usuario@servidor.universidad.edu
```

Conecta a máquina remota.

**Te pedirá contraseña** (o usará clave SSH si la configuraste).

Una vez dentro, estás en la terminal del servidor remoto.

### 11.2 SCP - Copiar Archivos

```bash
scp archivo.txt usuario@servidor:/ruta/destino/
```

Copia archivo local a servidor.

```bash
scp usuario@servidor:/ruta/archivo.txt .
```

Copia desde servidor a tu máquina actual (`.` = directorio actual).

```bash
scp -r directorio/ usuario@servidor:/ruta/
```

Copia directorio completo (recursivo).

---

## 12. CONCLUSIÓN: LA TERMINAL NO ES OPCIONAL

### No Es "Difícil", Es Diferente

La terminal asusta porque:
- No hay botones
- No hay confirmación visual
- Los errores no son amigables

Pero una vez que entiendes la lógica:
- Es más rápido que cualquier interfaz gráfica
- Es escalable (funciona igual para 1 archivo o 10,000)
- Es reproducible (el script es la documentación)

### La Terminal Es Tu Superpoder

Cuando domines estos comandos:
- Podrás procesar genomas completos
- Podrás automatizar pipelines complejos
- Podrás trabajar en servidores remotos sin limitaciones
- Podrás leer papers de bioinformática sin sentirte perdido

### Unix No Es El Enemigo

Unix es el lenguaje que las máquinas entienden cuando les pides hacer ciencia a escala.

Y ahora tú también lo entiendes.

---

## EJERCICIOS

### Navegación y Manipulación

1. Crea una estructura de directorios para un proyecto:
   ```
   proyecto_genoma/
   ├── data/
   │   ├── raw/
   │   └── processed/
   ├── scripts/
   └── results/
   ```

2. Descarga un archivo FASTA de NCBI y:
   - Cuenta cuántas secuencias tiene (líneas con `>`)
   - Extrae los primeros 10 headers
   - Guarda solo las secuencias (sin headers) en otro archivo

### Procesamiento de Texto

3. Dado un archivo VCF:
   - Elimina todas las líneas de comentario (`#`)
   - Extrae solo la columna de cromosoma y posición
   - Cuenta cuántas variantes hay en el cromosoma 1

4. Script de análisis rápido:
   - Lee un archivo FASTQ
   - Cuenta reads totales
   - Calcula el porcentaje de bases N
   - Output: resumen en formato tabular

### Automatización

5. Crea un script que:
   - Tome todos los `.fastq` de un directorio
   - Calcule calidad con FastQC
   - Mueva los reports a una carpeta `qc_reports/`
   - Genere un log con fecha y hora

---

**FIN DEL CAPÍTULO 0A**

*Siguiente: Capítulo 0B - Python Para Bioinformática*
