#!/bin/bash

#SBATCH --account=emm2
#SBATCH --job-name=hmmsearch
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=24
#SBATCH --output=data/logs/hmmsearch_aleix_%A_%a.out
#SBATCH --error=data/logs/hmmsearch_aleix_%A_%a.err
#SBATCH --array=1-56%5

module load hmmer

SAMPLE=$(cat data/genomas_pH/names_56.txt | awk "NR == ${SLURM_ARRAY_TASK_ID}") # 165

FASTA=data/genomas_pH/prodigal_out/${SAMPLE}.faa

OUT_DIR="data/genomas_pH/hmmer_loop_1/${SAMPLE}"

HMM_DIR="data/K00016.hmm"

mkdir -p ${OUT_DIR}

for HMM_PROFILE in ${HMM_DIR}/*.hmm; do
PROFILE_NAME=$(basename ${HMM_PROFILE} .hmm)
HMM_TBLOUT="${OUT_DIR}/${SAMPLE}_${PROFILE_NAME}.tblout"
HMM_DOMTBLOUT="${OUT_DIR}/${SAMPLE}_${PROFILE_NAME}.domtblout"

echo "Ejecutando hmmsearch con ${PROFILE_NAME} para muestra ${SAMPLE}"

## hmmsearch

hmmsearch \
  --domtblout ${HMM_DOMTBLOUT} \
  --tblout ${HMM_TBLOUT} \
  --cpu ${SLURM_CPUS_PER_TASK} \
  ${HMM_PROFILE} \
  ${FASTA
