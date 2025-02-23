---
layout: post
date: 2024-12-27
title: Accessing A Wealth Of Genome Sequences On NCBI
tag: Daily tasks
categories: [Bioinformatics]
---

It began with a simple question: How has a particular gene evolved across insect genomes? To do this, I needed access to all publicly available insect genome sequences. Fortunately, the NCBI provides an excellent command-line tool datasets, accessing and processing this data is straightforward.
<!--more-->

#### Step 1: Getting a List of Insect Genomes

To start, I needed to generate a comprehensive list of insect genomes available on NCBI. Using the datasets command-line tool, I fetched a summary of all genomes under the taxon ID 50557 (insects) that were marked as the latest versions.

Here’s the code to retrieve the data and extract the key information:
```shell
# Download the genome summary for taxon 50557 in JSON format
datasets summary genome taxon 50557 --assembly-version "latest" | jq . > raw_all.json

# Extract relevant details and save to a tab-delimited file
echo -e "Organism\tTax_id\tAccession\tAssembly_Level\tSequencing_tech\tGenome_coverage\tContig_n50\tTotal_sequence_length\tRelease_date" > all_insects.txt

jq -r '.reports[] | [
    .organism.organism_name,
    .organism.tax_id,
    .accession,
    .assembly_info.assembly_level,
    .assembly_info.sequencing_tech,
    .assembly_stats.genome_coverage,
    .assembly_stats.contig_n50,
    .assembly_stats.total_sequence_length,
    .assembly_info.release_date
] | @tsv' raw_all.json >> all_insects.txt
```

#### Step2: Filtering for high-quality genomes

Since some species have multiple genome versions (e.g., scaffold-level and chromosome-level assemblies), I filtered the data to prioritize:

- Chromosome-level assemblies where available.
- Genomes with the highest sequencing depth.

This filtering step ensures the highest quality data for downstream analysis. The Python script below performed the filtering:
```shell
# Filter the genome list for high-quality assemblies 
python filter.py all_insects.txt all_insects_filtered.txt
```

#### Step 3: Downloading the Genomes

With the filtered list in hand, I downloaded the genomes using their accession numbers. The key tool for this step is `datasets download genome accession`, which simplifies fetching large datasets from NCBI.

#### Step 4: Mapping Gene Sequences

After downloading the genomes, I mapped my gene sequences to these assemblies. For this, I used:
- Minimap2 for aligning DNA sequences.
- Miniprot for aligning protein sequences.

These tools are efficient and reliable for mapping sequences, even across large genomic datasets.
```shell
# target genes
cds='/data/sp11/nc/RefGeneSeq/S.gregaria.BBP.cds.clean.fa'
protein='/data/sp11/nc/RefGeneSeq/S.gregaria.BBP.protein.clean.fa'

# Read the corresponding line from the input file
input_line=$(awk "NR==$SLURM_ARRAY_TASK_ID" 02.species.latest.list)
read -r order family genus species accessionNum <<< "$input_line"

# Define the folder structure
folder="/data/sp11/nc/genomesLatest/$order/$family/$genus/$species"
mkdir -p "$folder" || { echo "Failed to create directory $folder"; exit 1; }
cd "$folder" || { echo "Failed to enter directory $folder"; exit 1; }

# Download and unzip genome data
if datasets download genome accession "$accessionNum" --filename "${accessionNum}.zip"; then
    unzip "${accessionNum}.zip"
    ref_full=`ls ncbi_dataset/data/*/$accessionNum*.fna`
    ref=$(basename $ref_full)
    mv ncbi_dataset/data/*/$accessionNum*.fna ./
    rm -rf "${accessionNum}.zip" ncbi_dataset md5sum.txt README.md
else
    echo "Failed to download or unzip genome for $accessionNum"
    exit 1
fi

species0="${ref%.fna}"

# dc-megablast
makeblastdb -in $ref -dbtype nucl -out $species0
blastOut="${accessionNum}.dcBlast"
rm_blast="${species0}.n*"
blastn -task dc-megablast -query $cds -db $species0 -out $blastOut -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore qlen slen" -num_threads 8 -max_target_seqs 6 -template_type "coding" -template_length 18 -word_size 11 -evalue 1e-5 && rm -f $rm_blast

miniMap2_index="${species0}.mmi"
alignment_sam="${accessionNum}.sam"
sorted_sam="${accessionNum}.sorted.sam"
sorted_bam="${accessionNum}.sorted.bam"

# Build Minimap2 index and align transcripts
/home/jiangc/minimap2-2.26_x64-linux/minimap2 -t8 -d "$miniMap2_index" "$ref"
if [ -f "$cds" ]; then
    /home/jiangc/minimap2-2.26_x64-linux/minimap2 -I 12G -t 8 -ax splice:hq -uf $ref $cds > $alignment_sam
    samtools sort -o "$sorted_sam" "$alignment_sam" && rm -f "$alignment_sam"
    samtools view -F 4 -bS "$sorted_sam" -o "$sorted_bam" && rm -f "$sorted_sam"
    if ! samtools index "$sorted_bam"; then
        echo "Error: samtools index failed for $sorted_bam"
    fi
else
    echo "Error: CDS file not found: $cds"
fi
rm -f "$miniMap2_index"

# Build Miniprot index and align protein sequences
miniprot_index="${species0}.mpi"
output_paf="${accessionNum}.paf"
/home/jiangc/miniprot/miniprot -t8 -d "$miniprot_index" "$ref"
if [ -f "$protein" ]; then
    /home/jiangc/miniprot/miniprot -t8 "$miniprot_index" "$protein" > "$output_paf"
else
    echo "Error: Protein file not found: $protein"
fi

# Cleanup unnecessary files
rm -f "$ref" "$miniprot_index" "${species0}.*"
# mv * ../ && cd ../ && rm -rf $folder
echo "Done! Processing complete for $accessionNum in $folder"
```