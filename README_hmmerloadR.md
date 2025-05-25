#!/bin/bash

#SBATCH --account=emm2
#SBATCH --job-name=hmmsearch
#SBATCH --ntasks=1
#SBATCH --cpus-per-task=24
#SBATCH --output=data/logs/R_format_hmmsearch_aleix_%A_%a.out
#SBATCH --error=data/logs/R_format_hmmsearch_aleix_%A_%a.err
#SBATCH --array=1-56%5

module load hmmer
module load R

SAMPLE=$(cat data/genomas_pH/names_56.txt | awk "NR == ${SLURM_ARRAY_TASK_ID}") # 165

FASTA=data/genomas_pH/prodigal_out/${SAMPLE}.faa

HMM_DIR="data/profiles/K20482.hmm"

OUT_DIR="data/genomas_pH/hmmer_loop_2/${SAMPLE}"

mkdir -p ${OUT_DIR}

for HMM_PROFILE in ${HMM_DIR}/*.hmm; do
PROFILE_NAME=$(basename ${HMM_PROFILE} .hmm)
HMM_TBLOUT="${OUT_DIR}/${SAMPLE}_${PROFILE_NAME}.tblout"
HMM_DOMTBLOUT="${OUT_DIR}/${SAMPLE}_${PROFILE_NAME}.domtblout"

TBL_OUT="${OUT_DIR}/${SAMPLE}_${PROFILE_NAME}_tblout.tsv"
DOMTBL_OUT="${OUT_DIR}/${SAMPLE}_${PROFILE_NAME}_domtblout.tsv"

Rscript scripts/genomas_pH/aleix_format_hmmer.R ${HMM_DOMTBLOUT} domtblout ${DOMTBL_OUT}
Rscript scripts/genomas_pH/aleix_format_hmmer.R ${HMM_TBLOUT} tblout ${TBL_OUT}
done
