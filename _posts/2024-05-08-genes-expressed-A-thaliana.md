---
layout: post
date: 2024-05-08
title: How Many Genes Are Expressed in Arabidopsis thaliana Leaves?
tag: Daily tasks
categories: [Bioinformatics]
---

When working with Iso-Seq data from different plant species’ leaves, I became curious about what genes are typically expressed in a plant’s leaves. Naturally, I thought about the well-studied model organism, Arabidopsis thaliana. However, I couldn’t find a study directly listing the genes expressed in its leaves—or perhaps I wasn’t looking in the right places. Fortunately, with the wealth of publicly available data, I could answer this question myself.
<!--more-->

The general strategy is to map RNA-seq reads from untreated Arabidopsis thaliana leaves to its genome to identify expressed genes. Below is a step-by-step guide to how I did it.

### Step 1: Download RNA-Seq Data and CDS
To begin, I need RNA-seq data from Arabidopsis thaliana leaves and its coding sequences (CDS) for alignment.

1. RNA-Seq Data: Use untreated leaf samples. Search NCBI’s Sequence Read Archive (SRA) for publicly available datasets. Be careful to select datasets with normal leaf tissue and no experimental treatments. The one I used is SRX17622838: GSM6589889: [Mock-treated Arabidopsis leaf](https://www.ncbi.nlm.nih.gov/sra/SRX17622838[accn]).

2. CDS Data: Download coding sequences from the [TAIR database](https://www.arabidopsis.org/).

### Step 2: Map RNA-Seq Data to CDS
The next step is to map the RNA-seq reads to the Arabidopsis thaliana CDS to determine gene expression. [Salmon](https://salmon.readthedocs.io/en/latest/salmon.html) is an ultra-fast, memory-efficient tool for quantifying transcript abundance. It performs lightweight alignment and is ideal for my purpose.

### Step 3: Analyze Gene Expression
Filter for genes with non-zero expression values (setting different thresholds) in the quant.sf file to identify genes expressed in Arabidopsis leaves.
<img src="/assets/img/Arabidopsis-thaliana-genes-leaves.jpg" style="display: flex; flex-direction: column; align-items: center; max-width: 100%; margin: 0 auto;">