# Transcriptomica_Introduccion-Analisis
Esta es una guía básica para la interpretación de datos de transcriptómica dirigida a estudiantes de Ingeniería en Biotecnología, con un grado básico de conocimiento en Bioinformático. 

Las ciencias ómicas son ramas de la biología que estudian, de manera integral, los distintos niveles de información en los organismos. Su enfoque es global, es decir, no analizan un solo gen, proteína o metabolito, sino todos los componentes al mismo tiempo para entender cómo funcionan en conjunto.

* Genómica: estudia la estructura, función, evolución y mapeo de los genomas completos de un organismo.

* Transcriptómica: analiza el conjunto total de ARN mensajeros (transcriptoma) que se producen bajo ciertas condiciones, como una infección o un estrés ambiental. Refleja qué genes están activos y en qué medida.

* Proteómica: se centra en las proteínas producidas, su abundancia, propiedades bioquímicas y roles funcionales en la célula.

* Metabolómica: examina los metabolitos, que son los productos de los procesos celulares, permitiendo entender el estado fisiológico del organismo.

## Transcriptómica 

Nuestro tema de hoy, la *transcriptómica*, es clave porque actúa como un puente entre el genoma y el fenotipo. Mientras que el ADN representa el potencial genético, el transcriptoma muestra qué genes están realmente en uso en un momento dado. Mediante técnicas como RNA-seq, podemos medir cuántas copias de ARN mensajero se producen para cada gen y esto permite: 

* Identificar genes inducidos durante una respuesta (por ejemplo, genes de defensa en plantas infectadas).

* Reconocer genes reprimidos cuando la célula redirige su energía a otros procesos.

* Descubrir nuevas variantes de transcritos o patrones de regulación específicos.

### I. "Pipeline" básico de transcriptómica

#### 0. Secuenciación de ARN.
* Se obtiene la secuencia de nucleótidos (bases: A, U, C, G) de los fragmentos de ARN convertidos en ADNc.
* Es un conjunto masivo de lecturas cortas (short reads, 50-200 pb) que representan el transcriptoma de la muestra en ese momento.
* **Producto:** archivos FASTQ, que son las secuencias comprimidas en archivos zip. Recomiendo realizar los análisis sin descomprimirlas. 

#### 1. Control de calidad – FastQC

* En esta etapa se evalúa la calidad de las lecturas crudas obtenidas por secuenciación.
* **Producto:** archivos html con estadísticas de calidad de secuencias. 
* Se revisa la calidad de bases (Phred score), presencia de adaptadores, contenido de guanina-citosina (GC) y distribución de longitudes.
* Permite decidir si es necesario un procesamiento adicional antes de los análisis.

#### 2. Pre-procesamiento – Trimmomatic

* Se eliminan adaptadores, bases de baja calidad y lecturas muy cortas.
* **Producto:** archivos FASTQ con menos lecturas. 
* El objetivo es obtener un conjunto de "secuencias limpias" y confiables para el análisis.
* Se recomienda repetir el control de calidad después de este paso, para garantizar que las secuencias obtenidas cumplen con los estándares de calidad. 

#### 3. Alineamiento de lecturas – HISAT2

* Las lecturas limpias se alinean al genoma de referencia del organismo de estudio.
* **Producto:** Se obtiene un archivo BAM con las posiciones exactas de cada lectura en el genoma.
* Revisar % de lecturas multimapeadas (genes repetitivos) y no alineadas (posible contaminación).
* Es deseable un alineamiento >85%. 

#### 4. Ensamblaje de transcritos – Trinity

* A partir de las lecturas alineadas, se reconstruyen los transcritos completos. Esto permite identificar genes conocidos, isoformas alternativas y transcritos no anotados previamente.
* **Producto:** archivos FASTA/GTF con transcritos ensamblados.
* El resultado es una visión más completa del transcriptoma expresado bajo las condiciones estudiadas.

#### 5. Cuantificación de expresión – FeatureCounts

* Se cuentan cuántas lecturas corresponden a cada gen o transcrito.
* **Producto:** una matriz de conteos crudos (genes × muestras), que sirve como base para los análisis estadísticos posteriores.

#### 6. Análisis de expresión diferencial – DESeq2 / edgeR

* Se aplican modelos estadísticos que corrigen diferencias en la profundidad de secuenciación y estiman la variabilidad biológica.
* Se realizan comparaciones entre grupos de muestras. 
* **Producto:** una lista de genes diferencialmente expresados con sus valores de cambio de expresión (log2FoldChange) y significancia estadística (p-ajustada).
* Los genes con mayor expresión en la condición experimental se clasifican como upregulated (inducidos) y los de menor expresión como downregulated (reprimidos).

### Visualización de resultados transcriptómicos. 

En estudios de transcriptómica, como el caso de maíz sano (Control, C) vs maíz inoculado con Colletotrichum graminicola (Tratamiento, T), una vez obtenidos los genes diferencialmente expresados, el siguiente paso es visualizar e interpretar esos resultados.

La visualización es clave porque:
* Permite identificar patrones globales de expresión.

* Ayuda a comprender el comportambiento de los datos. 

* Facilita la interpretación biológica al conectar los cambios en la expresión con procesos celulares y rutas metabólicas.

Existen muchas representaciones gráficas posibles: volcano plots, heatmaps, análisis de enriquecimiento (GO/KEGG), redes de interacción, entre otros. Cada una ofrece una perspectiva distinta de los datos. Para esta sesión, nos enfocaremos en tres visualizaciones fundamentales:

* **PCA (Análisis de Componentes Principales):** permite ver si las muestras se separan según la condición (C vs T).

* **Heatmap (mapa de calor):** muestra los genes más variables o diferencialmente expresados y cómo se agrupan las muestras en función de su expresión.

* **COG (Clusters de grupos ortólogos):** clasifica genes diferencialmente expresados en categorías funcionales, para analizar qué procesos biológicos son más afectadados por el factor de estudio.

