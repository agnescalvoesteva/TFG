# TFG
Los scripts usados para el TFG "Análisis de datos de genomas bacterianos para la búsqueda de genes asociados al pH vaginal"
#Barrnap#
#!/bin/bash

#SBATCH --account=emm2
#SBATCH --job-name=barrnap
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=12
#SBATCH --output=data/logs/barrnap_%J.out
#SBATCH --error=data/logs/barrnap_%J.err
#SBATCH --array=1-56%8

module load barrnap
module load samtools  # Añadido para generar índices

SAMPLE=$(cat data/samples_filee.txt.save | awk "NR == ${SLURM_ARRAY_TASK_ID$FASTA=data/genomas_pH/Genomas_gtdb/Genomas_1/${SAMPLE}.fna
OUT_DIR=data/tree_ph_2
OUT_NAME=${OUT_DIR}/tree_pH_${SAMPLE}

# Crear directorio de salida si no existe (ignora el error si ya existe)
mkdir -p ${OUT_DIR}

# Generar índice .fai si no existe
if [ ! -f "${FASTA}.fai" ]; then
    samtools faidx ${FASTA}
fi

barrnap \
    --kingdom bac \
    --threads ${SLURM_CPUS_PER_TASK} \
    --outseq ${OUT_NAME}.fna \
    ${FASTA} \
    > ${OUT_NAME}.gff
    
