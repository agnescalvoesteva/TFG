#prodigal#
#!/bin/sh

#SBATCH --account=emm2
#SBATCH --job-name=prodigal
#SBATCH --mem=50G
#SBATCH --ntasks-per-node=1
#SBATCH --cpus-per-task=24
#SBATCH --output=data/logs/prodigal_%A_%a.out
#SBATCH --error=data/logs/prodigal_%A_%a.err
#SBATCH --array=1-157%5

module load prodigal/2.6.3

OUT_DIR=data/genomas_pH/prokka

mkdir -p ${OUT_DIR}

SAMPLE=$(cat data/samples_file.txt | awk "NR == ${SLURM_ARRAY_TASK_ID}") # $
QUERY_FILE="data/157_scaffolds/${SAMPLE}_scaffolds.fasta"
if [[ ! -f "${QUERY_FILE}" ]]; then
    echo "Error: Archivo de entrada ${QUERY_FILE} no encontrado" >&2
    exit 1
fi
prodigal -q -p single \
        -a ${OUT_DIR}/${SAMPLE}.faa \
        -d ${OUT_DIR}/${SAMPLE}.fna \
        -f gff \
        -i ${QUERY_FILE} \
        -o ${OUT_DIR}/${SAMPLE}.gff
