# Universidad UNIR
## Materia: Introducción a la programación científica
## Repositorio BioBreastcancerAnalyse
### Actividad grupal
#### Integrantes: Betty Lucía Cruz Quinzo, Marcos Diego Barrionuevo Cordonez, Zynnia Leonor Echeverría Vergara, Darling Sugey Balón Cortez.
El siguiente repositorio es creado para la actividad grupal de la materia de introducción a la programación científica en el cual se subirá y modificará el dataset de ejemplo provisto por qiime2 utilizado en la actividad 2 de secuenciación y ómicas de proxima generación

## La actividad es sobre el Análisis de microbioma que corresponde a la matería de Ómicas y Secuenciación.
### Objetivo:  
El objetivo de esta actividad es seguir los pasos para el análisis de datos de microbioma para entender en qué consiste cada uno, y poder sacar conclusiones de los datos obtenidos. 
### Instruccion para el desarrollo de esta actividad
### 1.- Instalar conda
Hay dos opciones para la instalación:
1.1 - Usar conda:
https://docs.qiime2.org/2023.5/install/native/#install-qiime-2-within-a-conda-environment
1.2 - Usar docker:
https://docs.qiime2.org/2023.5/install/virtual/docker/
### Crear un directorio para nuestros datos:
mkdir qiime2-atacama-tutorial
### Ingresar al directorio:
cd qiime2-atacama-tutorial
### Descargar metadatos:
Manualmente: recuerde guardar los datos en el directorio que creamos, y guardar el archivo con el nombre “sample_metadata.tsv”:
https://data.qiime2.org/2023.5/tutorials/atacama-soils/sample_metadata.tsv
Desde el terminal de comandos con wget o curl:
wget -O "sample-metadata.tsv" "https://data.qiime2.org/2023.5/tutorials/atacama-soils/sample_metadata.tsv"
curl -sL "https://data.qiime2.org/2023.5/tutorials/atacama-soils/sample_metadata.tsv" >"sample-metadata.tsv"
#### Descargar secuencias:
Crear directorio para las secuencias:
mkdir emp-paired-end-sequences
#### Descargar secuencias manualmente:
https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/forward.fastq.gz
https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/reverse.fastq.gz
https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/barcodes.fastq.gz
Descargar con wget:
wget -O "emp-paired-end-sequences/forward.fastq.gz"
"https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/forward.fastq.gz"
wget -O "emp-paired-end-sequences/reverse.fastq.gz"
"https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/reverse.fastq.gz"
wget -O "emp-paired-end-sequences/barcodes.fastq.gz"
"https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/barcodes.fastq.gz"
#### O curl:
curl -sL "https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/forward.fastq.gz" > "emp-
paired-end-sequences/forward.fastq.gz"
curl -sL "https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/reverse.fastq.gz" > "emp-
paired-end-sequences/reverse.fastq.gz"
curl -sL "https://data.qiime2.org/2023.5/tutorials/atacama-soils/10p/barcodes.fastq.gz" >
"emp-paired-end-sequences/barcodes.fastq.gz"
#### 2.- Crear artefacto de qiime2 con las secuencias:
Si recuerdas, qiime2 trabaja con archivos llamados artefactos, por lo que luego de descargar las secuencias debemos crear un artefacto con ellas.
Crear artefacto:
qiime tools import --type EMPPairedEndSequences --input-path emp-paired-end-sequences
--output-path emp-paired-end-sequences.qza
Archivo obtenido:
emp-paired-end-sequences.qza
#### 3.- Demultiplex reads:
Este paso consiste en asociar las secuencias a las muestras correspondientes. Para esto necesitamos el archivo de metadatos, y debemos indicar la columna que contiene los “barcodes” de cada secuencia (esta columna se llama “barcode-equence”). En este caso, los reads de los barcodes son el complemento inverso de los presentes en el archivo de metadatos, por lo que incluimos la opción ‘’p-rev-
com-mapping-barcodes”.
qiime demux emp-paired --m-barcodes-file sample-metadata.tsv --m-barcodes-
column barcode-sequence --p-rev-comp-mapping-barcodes --i-seqs emp-paired-end-sequences.qza --o-per-sample-sequences demux-full.qza --o-error-correction-details demux-details.qza
#### 4.- Crear una submuestra:
En este caso, solo estamos realizando este paso con fines demostrativos. Usualmente, uno debe tener una justificación para crear un subconjunto de sus datos. Seleccionaremos el 30 % de los datos presentes en la muestra.
Crear submuestra:
qiime demux subsample-paired --i-sequences demux-full.qza --p-fraction 0.3 --o- subsampled-sequences demux-subsample.qza
Crear visualización para conocer cuantas secuencias tenemos por muestra:
qiime demux summarize --i-data demux-subsample.qza --o-visualization demux- subsample.qzv
Archivo obtenido:
demux-subsample.qza
Visualización obtenida (recuerda que puedes visualizar los archivos qzv en https://view.qiime2.org/): 
demux-subsample.qzv
#### 5.- Filtrar muestras que tienen menos de 100 reads:
En la visualización obtenida en el paso anterior podemos ver que algunas muestras tienen menos de 100 reads. Esto se considera una cobertura insuficiente, por lo que filtraremos estas muestras.
qiime tools export --input-path demux-subsample.qzv --output-path ./demux-subsample/
qiime demux filter-samples --i-demux demux-subsample.qza --m-metadata-file ./demux-
subsample/per-sample-fastq-counts.tsv --p-where 'CAST([forward sequence count] AS INT)> 100' --o-filtered-demux demux.qza
#### 6.- Revisar calidad y realizar “denoising”:
En este paso cortaremos las 13 primeras bases de cada read (pueden pertenecer a partidores y adaptadores) y seleccionaremos las secuencias de un largo de 150 nucleótidos.
qiime dada2 denoise-paired --i-demultiplexed-seqs demux.qza --p-trim-left-f 13 --p-trim-left-r
13 --p-trunc-len-f 150 --p-trunc-len-r 150 --o-table table.qza --o-representative-sequences
rep-seqs.qza --o-denoising-stats denoising-stats.qza
Archivos obtenidos:
table.qza (tabla de OTUs, aquí llamados “features”)
rep-seqs.qza (Secuencias de los OTUs)
denoising-stats.qza (estadísticas de la remoción de ruido)
Podemos generar resúmenes de estos archivos:
qiime feature-table summarize --i-table table.qza --o-visualization table.qzv --m-sample-metadata-file sample-metadata.tsv
qiime feature-table tabulate-seqs --i-data rep-seqs.qza --o-visualization rep-seqs.qzv
qiime metadata tabulate --m-input-file denoising-stats.qza --o-visualization denoising-stats.qzv
#### 7.- Generar un árbol para el análisis de diversidad filogenética:
iime2 permite generar diferentes métricas de diversidad, algunas de las cuales necesitan de árboles filogenéticos para calcular los índices. Alinearemos las secuencias con mafft, y FastTree para la generación del árbol sin raíz, y luego se agregará la raíz.
qiime phylogeny align-to-tree-mafft-fasttree --i-sequences rep-seqs.qza --o-alignment
aligned-rep-seqs.qza --o-masked-alignment masked-aligned-rep-seqs.qza --o-tree unrooted-tree.qza --o-rooted-tree rooted-tree.qza
#### 8.- Cálculo de diversidad alfa y beta:
Calcularemos varias métricas de diversidad y generaremos gráficos de PCoA con los datos obtenidos. Las métricas que calcularemos son:
##### - Diversidad alfa
• Índice de diversidad de Shannon (medición cuantitativa de riqueza de la comunidad)
• Características observadas (medición cualitativa de riqueza de la comunidad)
• Diversidad filogenética de Faith (medición cualitativa de riqueza de la
comunidad que incorpora relaciones filogenéticas entre “features”)
• Igualdad (o Igualdad de Pielou; medición de la igualdad de la comunidad)
##### - Beta diversity
• Distancia de Jaccard (medida cualitativa de disimilitud de la comunidad)
• Bray-Curtis distance (medida cuantitativa de disimilitud de la comunidad)
• unweighted UniFrac distance (medida cualitativa de disimilitud de la comunidad que incorpora relaciones filogenéticas entre “features”)
• weighted UniFrac distance (medida cuantitativa de disimilitud de la comunidad que incorpora relaciones filogenéticas entre “features”)
##### Analizar: 
¿Qué profundidad de muestreo debemos seleccionar? Observa cuántas secuencias tienen cada muestra visualizando la tabla table.qzv y selecciona la
profundidad de muestreo (pista: ¿cuáles son los valores menores y mayores de secuencias en una muestra? ¿Cuál es la mediana? ¿Y el promedio? ¿Qué valor permitirá que mantengamos la mayor cantidad de muestras posibles?). Según este número realizaremos el paso de rarefacción de muestras
#### Rarefacción y cálculo de diversidad (reemplaza el número que sigue a “p-sampling-depth” con el que tú has seleccionado):
qiime diversity core-metrics-phylogenetic --i-phylogeny rooted-tree.qza --i-table table.qza --p- sampling-depth 1103 --m-metadata-file sample-metadata.tsv --output-dir core-metrics-result
Luego, podemos agrupar los reultados según metadatos (luego de “--i-alpha-diversity” puedes cambiar el archivo por el de otra métrica de diversidad, por ejemplo “core-metrics-results/bray_curtis_pcoa_results.qza”):
qiime diversity alpha-group-significance --i-alpha-diversity core-metrics-results/faith_pd_vector.qza --m-metadata-file sample-metadata.tsv --o-visualization core-metrics-results/faith-pd-group-significance.qzv
El nombre de la visualización obtenida se verá así (“nombre-de-métrica-significance.qzv”):
core-metrics-results/faith-pd-group-significance.qzv
PREGUNTA: ¿Qué categorías de metadatos están asociadas con más fuerza con las
diferencias en riqueza de la comunidad? ¿Y con igualdad?
##### Análisis PERMANOVA de diversidad beta:
Aquí, podemos cambiar las variables “--i-distance-matrix” por otra métrica de beta-diversidad y “--m-metadata-column” por el nombre de una columna de metadatos de interés. Puedes probar diferentes columnas que te creas que podrían afectar la composición microbiana.
qiime diversity beta-group-significance --i-distance-matrix core-metrics-results/unweighted_unifrac_distance_matrix.qza --m-metadata-file sample-metadata.tsv --m-metadata-column transect-name --o-visualization core-metrics-results/unweighted-unifrac-body-site-significance.qzv --p-pairwise
##### Gráfico PCoA:
qiime emperor plot --i-pcoa core-metrics-results/unweighted_unifrac_pcoa_results.qza --m-metadata-file sample-metadata.tsv --p-custom-axes days-since-experiment-start --o-visualization core-metrics-results/unweighted-unifrac-emperor-days-since-experiment-start.qzv
Visualización obtenida:
core-metrics-results/unweighted-unifrac-emperor-days-since-experiment-start.qzv
#### 9.- Análisis taxonómico:
Clasificaremos nuestros “features” de acuerdo a la taxonomía de la base de datos Greengenes. Para esto, hay que descargar la base de datos:
Descarga manual:
https://data.qiime2.org/2023.5/common/gg-13-8-99-515-806-nb-classifier.qza
Descarga con wget:
wget -O "gg-13-8-99-515-806-nb-classifier.qza" "https://data.qiime2.org/2023.5/common/gg-
13-8-99-515-806-nb-classifier.qza"
Descarga con curl:
curl -sL "https://data.qiime2.org/2023.5/common/gg-13-8-99-515-806-nb-classifier.qza" >
"gg-13-8-99-515-806-nb-classifier.qza"
Clasificación taxonómica:
qiime feature-classifier classify-sklearn --i-classifier gg-13-8-99-515-806-nb-classifier.qza --i-
reads rep-seqs.qza --o-classification taxonomy.qza
Crear tabla con taxonomía:
qiime metadata tabulate --m-input-file taxonomy.qza --o-visualization taxonomy.qzv
Archivos obtenidos:
taxonomy.qza
gg-13-8-99-515-806-nb-classifier.qza
Visualizaciones obtenidas:
taxonomy.qzv
#### PREGUNTA: ¿Qué pasa si evaluamos algunas de las secuencias con BLAST? ¿Son las
clasificaciones taxonómicas diferentes a las de qiime2? ¿A qué nivel taxonómico surgen las diferencias?
#### 10.- Análisis diferencial de abundancia microbiana:
Preparar tabla:
qiime composition add-pseudocount --i-table table.qza --o-composition-table comp-table.qza
Archivo obtenido:
comp-table.qza
Correr AMCO (para análisis diferencial):
qiime composition ancom --i-table comp-table.qza --m-metadata-file sample-metadata.tsv --m-metadata-column subject --o-visualization ancom-subject.qzv
Visualización obtenida:
ancom-subject.qzv
Agregar taxonomía:
Aquí podemos cambiar la variable “‘p-level” por otro número, para tener otro nivel taxonómico.
qiime taxa collapse --i-table table.qza --i-taxonomy taxonomy.qza --p-level 6 --o-collapsed-table table-l6.qza
qiime composition add-pseudocount --i-table gut-table-l6.qza --o-composition-table comp-gut-table-l6.qza
qiime composition ancom --i-table comp-gut-table-l6.qza --m-metadata-file sample-metadata.tsv --m-metadata-column subject --o-visualization l6-ancom-subject.qzv
Archivos obtenidos:
gut-table-l6.qza
comp-gut-table-l6.qza
