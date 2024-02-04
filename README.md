# BioBreastcancerAnalyse
## Materia: Introducción a la programación científica
### Actividad 2 
#### Integrantes: Betty Lucía Cruz Quinzo, Marcos Diego Barrionuevo Cordonez, Zynnia Echeverría, Darling Balón.
El siguiente repositorio es creado para la actividad grupal de la materia de introducción a la programación científica en el cual se subirá y modificará el dataset libre de breastcancer del enlace: https://archive.ics.uci.edu/dataset/15/breast+cancer+wisconsin+original
La actividad que hemos subido es de la materia de Ómicas y Secuenciación con el tema: Análisis de microbiota. 
### Objetivo:  
El objetivo de esta actividad es seguir los pasos para el análisis de datos de microbioma para entender en qué consiste cada uno, y poder sacar conclusiones de los datos obtenidos. 
### Instruccion para el desarrollo de esta actividad
### 1.- Instalar conda
Hay dos opciones para la instalación:
1.1 - Usar conda:
https://docs.qiime2.org/2023.5/install/native/#install-qiime-2-within-a-conda-
environment
1.2 - Usar docker:
https://docs.qiime2.org/2023.5/install/virtual/docker/
### Crear un directorio para nuestros datos:
mkdir qiime2-atacama-tutorial
### Ingresar al directorio:
cd qiime2-atacama-tutorial
### 2 Descargar metadatos:
Puedes hacerlo manualmente: recuerda guardar los datos en el directorio que
creamos, y guardar el archivo con el nombre “sample_metadata.tsv”:
https://data.qiime2.org/2023.5/tutorials/atacama-soils/sample_metadata.tsv
O desde el terminal de comandos con wget o curl:
wget -O "sample-metadata.tsv" "https://data.qiime2.org/2023.5/tutorials/atacama-
soils/sample_metadata.tsv"
curl -sL "https://data.qiime2.org/2023.5/tutorials/atacama-soils/sample_metadata.tsv" >
"sample-metadata.tsv"
Descargar secuencias:
Crear directorio para las secuencias:
mkdir emp-paired-end-sequences
Descargar secuencias manualmente:
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
O curl:
