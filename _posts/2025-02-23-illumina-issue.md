---
layout: post
date: 2025-02-23
title: Issues with Illumina Sequencing -- A Case Study
tag: Daily tasks
categories: [Bioinformatics]
---
We recently encountered problems with our Illumina sequencing results. We ordered a full-lane sequencing run, which should have produced 1000G of total output, but the actual yield was only 700G. Besides, our sequencing involved 22 species and over 3000 individuals, using dual indices (i5 and i7, 8 bp each). However, more than 200G of reads were not assigned to any species.
<!--more-->

The sequencing platform and flow cell used is NovaSeq X Plus and 25B (8 lanes, 784 tiles per lane).
### 1. Missing data
To diagnose the issue, we requested the base calling data (BCL files) from the sequencing provider rather than just the FASTQ data. The total volume of base calling signal data was significantly smaller than the expected yield. We converted the BCL files to FASTQ using `bcl-convert`, as the sequencing provider did. 

#### BCL to FASTQ Conversion:
```shell
bcl-convert \
          --bcl-input-directory /data/sp11/rawData/rawData2025II/250213_LH00452_0169_A22LMNGLT4 \
          --output-directory /data/sp11/rawData/rawData2025II/fastQ/result \
          --sample-sheet /data/sp11/rawData/rawData2025II/fastQ/SampleSheet.csv \
          --barcode-mismatches 0
```
The conversion took several hours. Afterward, we analyzed the tile-level statistics using the `FastqSummaryF1L3.txt` file in the `Stats` folder.

#### Tile-Level Analysis:
```R
library(tidyverse)
df_lane = read_delim("FastqSummaryF1L3.txt")

df_tiles <- df_lane %>% 
    group_by(Tile) %>% 
    summarise(ReadsRawTotal = sum(NumberOfReadsRaw),
              ReadsPFTotal = sum(NumberOfReadsPF)) %>% 
    mutate(per = ReadsPFTotal/ReadsRawTotal)
View(df_tiles)
```
<figure style="display: flex; flex-direction: column; align-items: center; max-width: 100%; margin: 0 auto;">
<img src="/assets/img/illumina_issue_R.png" style="display: flex; flex-direction: column; align-items: center; max-width: 100%; margin: 0 auto;">
<figcaption style="text-align: center; margin-top: 8px;">screen shot of df_tiles</figcaption>
</figure>
<p></p>

To understand the data present in `df_tiles`, we need to first understand **Chastity filtering** and the **PF clusters** (in the table or `FastqSummaryF1L3.txt` is called ReadsPF, since one cluster will output as one read). 

##### What is a PF Cluster?

PF (Pass Filter) clusters meet Illumina's quality criteria. Only PF clusters are included in the final FASTQ output Clusters failing the filter due to low intensity, poor focus, or mixed signals are discarded.

##### Filtering Mechanism

The chastity filter is based on the ratio of the strongest fluorescence intensity to the second strongest intensity. A cluster fails if:

$$\frac{\text{highest intensity}}{\text{highest + second highest intensity}} < 0.6$$

during the first 25 cycles.

**Clusters are removed due to**:

- Weak fluorescence (low DNA copies, weak signal)

- Mixed clusters (two templates in one spot, confused signal)

- Out-of-focus clusters (unreliable signal detection)

**According to the `df_tiles`**, 

- Ideally, each tile should produce around 6,505,184 clusters, which corresponding to 5,100,064,256 for the full lane.

- 4 out of 784 tiles showed almost no PF clusters at all (Tiles 1424, 1425, 2424, and 2425).

- The rest had around 45% PF clusters, leading to fewer reads.

- The missing 300G is due to missing clusters, likely caused by a library issue rather than a machine or flow cell defect.

##### Map of clusters 
The 55% missing clusters could due to weak/confused signal, but it could also due to the fact there is no clusters forming on the tile from the beginning. Illumina sequencers NovaSeq X Plus will first creates a "map" of where every cluster is located on the flow cell. Later, during each sequencing cycle, the instrument only interrogates those pre-identified coordinates. We can look into this 'map' to see at least how many clusters are formed before entering the sequencing step. This map information is stored in *.locs file. Usually there are also *.filter along with the BLC data. These *.filter files contains information on which clusters passed the quality filter (PF).


According to [Gabriel R.](https://www.biostars.org/p/51681/#51780), 
> The .locs file are structure as follows:
>
> The last 4 bytes of the first 12 bytes give you the # of clusters as an unsigned int. The remaining sets of 8 bytes give you 2 floats, one x coordinate, one y. The exact coordinates are obtained by round( 10 x [raw coordinate] + 1000)


```python
import numpy as np
import struct

# Path to your .locs file
file_path = 's.locs'  

# Read the binary file
with open(file_path, 'rb') as file:
    data = file.read()

# Extract the number of clusters (last 4 bytes of the first 12 bytes)
num_clusters = struct.unpack(">I", data[8:12])[0]  # unsigned int (4 bytes)
print(f"Number of clusters: {num_clusters}")
```
There are 3,762,447,104 clusters (while 5,100,064,256 are expected) enter the sequencing stage. According to chatGPT, the lower-than-expected cluster density could be due to factors like:
- Under-loading of the flow cell (too little template DNA).
- Inefficient amplification of the clusters.
- Issues with the library preparation (e.g., low DNA concentration).
- Problems with reagent quality or sequencing chemistry.

The next question is then <span style="color: red;">would loading more material to the machine help relieve the problem</span>?

### 2. Undetermined reads
The undetermined reads are caused by the fact the indices are not matching the target indices. We first look at the distribution of the indices.
<div class="image-row">
  <img src="/assets/img/illumina_issue_i5_dis.png " alt="Image 1" class="image">
  <img src="/assets/img/illumina_issue_i7_dis.png" alt="Image 2" class="image">
</div>
<p></p>

We can see from the screenshots above that:
- The `GGGGGGGG` is among the top 3 in both i5 and i7 sequence; in fact, more than 60% of undetermined reads have one or both its indices as `GGGGGGGG`. The NovaSeq X Plus uses a two-color system (see the detail below), where 'G' represents a dark signal, potentially explaining these indices.

- Our target indices are among the highest hits; this indicates **we could potentially rescue some of these reads by using only one index**.

<figure style="display: flex; flex-direction: column; align-items: center; margin: 0 auto;">
<img src="/assets/img/illumina_issue_sequencer.png" style="display: flex; flex-direction: column; align-items: center; max-width: 100%; margin: 0 auto;">
<figcaption style="text-align: center; margin-top: 0px;">
  <a href="https://knowledge.illumina.com/instrumentation/novaseq-x-x-plus/instrumentation-novaseq-x-x-plus-reference_material-list/000007970">NovaSeq X Series</a>
</figcaption>
</figure>

<p></p>
<hr>
Before rescue these undetermined data, we should have a look at its quality. Surprisingly, undetermined reads showed rather good quality scores though the variation is quite large for each base. Could it the dark 'G' at each position caused the variation in base quality?
<figure style="display: flex; flex-direction: column; align-items: center; margin: 0 auto;">
<img src="/assets/img/illumina_issue_undeterminedR2_per_base_quality.png" style="display: flex; flex-direction: column; align-items: center; max-width: 100%; margin: 0 auto;">
<figcaption style="text-align: center; margin-top: 8px;">Per base quality of undetermined R2 </figcaption>
</figure>
<p></p>

We extract reads with indices `GGGGGGGG+GGGGGGGG`, and also at least 100 continuous 'G'in the reads, and look at their quality. Even more surprise, the base quality is even better and less variation (I would say it is almost perfect). 
<figure style="display: flex; flex-direction: column; align-items: center; margin: 0 auto;">
<img src="/assets/img/illumina_issue_100G_per_base_quality.png" style="display: flex; flex-direction: column; align-items: center; max-width: 100%; margin: 0 auto;">
<figcaption style="text-align: center; margin-top: 0px;">Per base quality of R2 with all 'G' indices </figcaption>
</figure>

We are certain that these reads are not real (both indices are 'GGGGGGGG' and at least 100 'G' within the read), but with such good qualtiy. This should alarm us that <span style="color: red;">**the two-color system sequencer is not doing what it claimed. It can really tell whether the dark signal is a real 'G' or no signal at all!**</span>.

<hr>
For our purpose, even a reads is assigned to a species, we still need to assign it to a specific individual.

We have compared the individual-level assignment as well. The individual assignment success is much lower in undetermined reads.

- Determined Reads: 23% ( not assigned to any individual)
- Undetermined Reads: 73.6% ( not assigned to any individual)


All in all the undetermined reads are overall bad quality.
<hr>
Nevertheless, we have decided to do what we can to rescue as many reads as possible using the following criteria:

- Use single-index assignment: Assign reads based on either i5 or i7. If a read is ambiguously assigned to multiple species, defer final assignment.
- Check barcode validity: Reads must have valid barcodes to be assigned.
- Assess base quality.
- Check for excessive 'G' sequences.
- Map reads to transcriptomes: To confirm species identity.






