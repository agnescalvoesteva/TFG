#rm(list = ls())

# List all .tsv files inside the hmmer folder
hmmer_files <- list.files("KO_allsample", pattern = "*.tsv", full.names = TRUE)

# Load thresholds and KO names only once
thresholds <- read_tsv("ko_list.unknown") |> 
  select(knum, threshold, score_type)

ko_names_list <- read_excel("KO_list.xlsx")

# Function to process one file
process_hmmer_file <- function(file) {
  hmmer <- read_tsv(file) |> 
    select(domain_name, query_name, sequence_evalue, sequence_score, domain_cevalue, domain_score, source_file)
  
  hmmer_with_thresholds <- hmmer |> 
    left_join(thresholds, by = c("query_name" = "knum")) |> 
    filter(
      (score_type == "full" & sequence_score >= as.numeric(threshold)) |
        (score_type == "domain" & domain_score >= as.numeric(threshold))
    ) |> 
    left_join(ko_names_list, by = c("query_name" = "KEGG")) |> 
    select(domain_name, query_name, PATHWAY, GENE_NAME, sequence_evalue, sequence_score, domain_cevalue, domain_score, threshold, score_type, source_file)
  
  return(hmmer_with_thresholds)
}
# Apply the function to all files and bind the results
all_hmmer_filtered <- map_dfr(hmmer_files, process_hmmer_file)


all_hmmer_filtered <- all_hmmer_filtered |> 
  separate(domain_name, into = c("Specie", "gene"), sep = "_(?=[^_]+$)")

all_hmmer_filtered$PATHWAY <- factor(all_hmmer_filtered$PATHWAY)

head(all_hmmer_filtered)

