Recently I wanted to construct a phylogenetic tree for a specific gene across multiple species. However, the gene in question has multiple copies within each species, making it challenging to identify the correct orthologs. The similarity among these copies complicates determining whether the best BLAST hit is truly the ortholog.
<!--more-->

One effective approach to address this challenge is using synteny, which is particularly well-suited for genomes with annotations (e.g., GFF files). Synteny analysis considers the conserved order of genes on a chromosome, providing additional evidence for orthology beyond sequence similarity.

Initially, I tried to identify orthologs manually (by visual inspection). While this provided a starting point, it quickly became clear that automating the process with specialized tools would be more efficient and reliable.

#### MCScanX for Automated Synteny Analysis
I used `MCScanX`, a tool designed for synteny and collinearity analysis. Below is an outline of the [workflow](https://www.nature.com/articles/s41596-024-00968-2):

##### Step 1: Prepare the Input Files
  **Download the data**: Obtain the protein sequences, CDS (coding sequences), and GFF files for the genomes of interest.
  ```shell
curl -OJX GET 'https://api.ncbi.nlm.nih.gov/datasets/v2/genome/accession/GCF_023864345.2/download?include_annotation_type=CDS_FASTA&include_annotation_type=PROT_FASTA&include_annotation_type=GENOME_ GFF&filename=GCF_023864345.2.zip' -H 'Accept: application/zip'
```
  **Fix GFF formatting**: Use the script `mkGFF3.pl` (found in the MCScanX_protocol repository, not included with MCScanX) to produce a corrected version of the GFF files compatible with MCScanX.

##### Step 2: Merge GFF Files
  Combine all the GFF files (of different species) into a single file. This step ensures all annotations are included for cross-species synteny analysis.

##### Step 3: Perform BLAST Searches
  Run `BLASTP` on the protein sequences of each species against all other species.
  ```shell
ref=$(awk "NR==$SLURM_ARRAY_TASK_ID" sp.list)
species=$(basename "${ref}" .protein.fa)
makeblastdb -in $ref -dbtype prot -out ncbiDB/$species

allSP=$(ls ncbi/*.protein.fa)
for sp in $allSP; do
    q=$(basename "$sp" .protein.fa)
    output="$q-TO-$species.blast"

    if [ "$q" == "$species" ]; then
        # Run BLASTp with 6 alignments for self-comparison
        blastp -db ncbiDB/"$species" -query "$sp" -evalue 1e-10 -num_alignments 6 \
               -outfmt 6 -out intermediateData/"$output" -num_threads 10
    else
        # Run BLASTp with 5 alignments for different species
        blastp -db ncbiDB/"$species" -query "$sp" -evalue 1e-10 -num_alignments 5 \
               -outfmt 6 -out intermediateData/"$output" -num_threads 10
    fi

done
```
  Merge all the BLAST results into a single file.

##### Step 4: Run MCScanX
  Use the merged GFF and BLAST files as inputs for MCScanX to identify collinear blocks and generate the final collinearity file.
  ```shell
MCScanX data/master -s 4  # when there are 4 genes in a row, called a Synteny
```

Finally grep the gene name from the the *.collinearity file.
