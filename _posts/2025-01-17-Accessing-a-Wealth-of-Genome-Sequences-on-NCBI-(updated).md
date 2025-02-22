---
layout: post
date: 2025-01-17
tag: Daily tasks
categories: [Bioinformatics]
---

There are almost 3000 insect species with genome sequences available, and I initially encountered errors when trying to download all the genomes at once. The error message incorrectly suggested that some accession numbers were invalid. To avoid this, I downloaded the genomes in smaller batches of 10 species at a time. After each batch, I ran the mapping process and waited for 5 minutes before starting the next batch.
<!--more-->

Here’s the updated code for this process:
```shell
#!/bin/bash

input_file="02.species.latest.all.list"
batch_size=10
total_rows=$(wc -l < "$input_file")  # Get the total number of rows in the file
batch_number=1  # Start with the first batch

# Loop through the file, processing 10 lines at a time
while [ $batch_number -le $((total_rows / batch_size)) ]; do
    # Extract the lines for the current batch
    start_line=$(( (batch_number - 1) * batch_size + 1))
    end_line=$(( batch_number * batch_size ))

    # Use 'sed' to get the batch of lines
    batch_data=$(sed -n "${start_line},${end_line}p" "$input_file")

    # Save the batch data to a temporary file
    echo "$batch_data" > "batch_${batch_number}.txt"

    # Submit the script to the cluster using the current batch data
    sbatch -a 1-$batch_size 02.align.latest.sh "batch_${batch_number}.txt"

    # Wait for 10 minutes before submitting the next batch
    sleep 300  # 5 minutes in seconds

    # Increment batch number for the next iteration
    batch_number=$((batch_number + 1))
done
```
