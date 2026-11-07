[![DOI](https://sandbox.zenodo.org/badge/1449743672.svg)](https://handle.test.datacite.org/10.5072/zenodo.636758)

# GA5 answers - Lillian Ehresmann

------------------------------------------------------------------------

## Part A: Your project dir, Git repo, and protocol

### 1: Fill in the top of the protocol

### 2: Markdown formatting

### 3: Create subdirs, and copy the scripts

```bash
# Your mkdir command: 
mkdir -p scripts data/ref results
```

### 4: Initialize a Git repository, and commit your first files

### 5: Download the reference transcriptome

Code to count the number of transcripts:

```bash
# Your command: 
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep -c ">"
```

The output of the command was:

```
28624
```

*Answer*: The number of transcripts in the file: 28,624

------------------------------------------------------------------------

## Part B: Read trimming

### 6: Run TrimGalore

*Answer*: Whether all batch jobs succeeded, and how you checked that
(and any problems you encountered, and how you solved them): The first issue I had was that I forgot how to run multiple bash blocks sequentially so I asked Claude for help (use semicolons to separate commands). Then, it ran. I did not recieve an email that a job failed. I further checked for the "Successfully finished" message using `grep -c`. There were 22 "Successfully finished" messages which matches the number of Garrigos samples. 

------------------------------------------------------------------------

## Part C: Transcript quantification with Salmon

### 7: Create the Salmon index

Code to check the size of the index dir:

```bash
# Your command:
du -sh results/salmonindex
```

The output of the command was:

```
1.1G    results/salmonindex/
```

*Answer*: How long it took to create the index, and how you found out: It took 21 seconds (Sat Nov  7 12:49:30 PM EST 2026 to Sat Nov  7 12:49:51 PM EST 2026; this was from the slurm log).

*Answer*: The size of the index dir: 1.1 gigabytes

### 8: Run Salmon for all samples

*I managed to get the Salmon output for all samples but I am not sure if I did it right as I did not code a loop (not sure if the directions were to write a loop or run each individually). I noticed that if I tried a loop and used a glob `*_R1.fastq.gz`, it wouldn't work because there was other files that had that in the name even if the name was longer, like _trimming_report.json*

### 9: The transcripts with the most reads

*Answer*: What the `NumReads` column contains: the estimated number of reads that are mapped to a transcript. 

```bash
# Your command:
cut -f 5 results/salmonquant/S63/quant.sf | sort -n | tail -n 4
```

The output of the command was:

```
4232.000
4774.749
8663.254
24004.889
```

### 10: The gene of the top transcript

```bash
# Your command:

# Find the transcript identifier in quant.sf for sample 63:
grep "24004.889" results/salmonquant/S63/quant.sf

# Find the FASTA header in the reference transcriptome file: 
zcat data/ref/GCF_016801865.2_TS_CPP_V2_rna.fna.gz | grep "XM_039591534.2"
```

The output of the commands was:

```
XM_039591534.2  6566    6315.086        14518.656821    24004.889
``` 

```
>XM_039591534.2 PREDICTED: Culex pipiens pallens vitellogenin-A1-like (LOC120426760), mRNA
```

*Answer*: The gene that the top transcript belongs to: vitellogenin-A1-like

### 11: Mapping rates

```bash
# Your command:
grep "mapping rate" results/salmonquant/*/logs/salmon_quant.log
```

The output of the command was:

```
results/salmonquant/S63/logs/salmon_quant.log:mapping rate: 82.4560%
results/salmonquant/S64/logs/salmon_quant.log:mapping rate: 81.9152%
results/salmonquant/S65/logs/salmon_quant.log:mapping rate: 79.1055%
results/salmonquant/S66/logs/salmon_quant.log:mapping rate: 81.9350%
results/salmonquant/S67/logs/salmon_quant.log:mapping rate: 83.0459%
results/salmonquant/S68/logs/salmon_quant.log:mapping rate: 78.8070%
results/salmonquant/S69/logs/salmon_quant.log:mapping rate: 82.1824%
results/salmonquant/S70/logs/salmon_quant.log:mapping rate: 82.3060%
results/salmonquant/S71/logs/salmon_quant.log:mapping rate: 79.9047%
results/salmonquant/S74/logs/salmon_quant.log:mapping rate: 78.2753%
results/salmonquant/S75/logs/salmon_quant.log:mapping rate: 80.3915%
results/salmonquant/S76/logs/salmon_quant.log:mapping rate: 82.3396%
results/salmonquant/S77/logs/salmon_quant.log:mapping rate: 82.3281%
results/salmonquant/S78/logs/salmon_quant.log:mapping rate: 84.2490%
results/salmonquant/S79/logs/salmon_quant.log:mapping rate: 80.4061%
results/salmonquant/S80/logs/salmon_quant.log:mapping rate: 82.1721%
results/salmonquant/S81/logs/salmon_quant.log:mapping rate: 83.6795%
results/salmonquant/S82/logs/salmon_quant.log:mapping rate: 78.7291%
results/salmonquant/S83/logs/salmon_quant.log:mapping rate: 76.1504%
results/salmonquant/S84/logs/salmon_quant.log:mapping rate: 83.2207%
results/salmonquant/S85/logs/salmon_quant.log:mapping rate: 76.9540%
results/salmonquant/S86/logs/salmon_quant.log:mapping rate: 83.0532%
```

*Answer*: The sample with the lowest mapping rate: S83 (76.1504%)

------------------------------------------------------------------------

## Part D: Summarizing all steps with MultiQC

### 12: Update your MultiQC script

### 13: Run MultiQC

*Answer*: For each program, the number of reports that MultiQC found, and why it found this number: For Salmon, it found 22 reports because it used both reads (R1 and R2) to produce a single output. For trimgalore, it found 44 reports because it processes/trims both reads individually resulting in double the outputs as there are samples. 

### 14: The General Statistics table

*Answer*: The sample with the lowest Salmon mapping rate, and whether this matches your answer to question 11: The General Statistics table indicates that S83 has the lowest Salmon mapping rate, which is the same as I determined in question 11 using the salmon_quant.log.

### 15: Adapter lengths

*Answer*: The most common adapter lengths, whether this surprised you,
and why you think these lengths are the most common: It looks to be around 1 to 4 bp lengths which makes sense because we saw most adapter-trimmed bases were 1 to 3 bp long in GA4. 

### 16: Ask Claude about the MultiQC report

*Answer*: What you asked, and what you learned from Claude's answer (in your own words): I asked it to explain what the Salmon fragment distribution plot means. Basically, for each of the 22 samples, there is a curve that indicates the normalized proportion of reads that are a certain length. In our case, most reads are between 150-200 bp which is apparently normal for RNA seq data. Moreover, each sample's curve appears to overall be a single peak which is consistent with better quality RNA (e.g. not degraded) and appropriate pre-processing (e.g. adapter trimming). Additionally, because the curves are normalized, we can compare samples and investigate variability in the curves potentially indicating issues with library prep step (when fragmentation occurs). 

------------------------------------------------------------------------

## Part E: Improving the workflow with genAI

### 17: Claude's review of your protocol and scripts

*Answer*: At least three of Claude's suggestions, and whether you agree or disagree with each one, and why:

1. Claude said there is a placeholder for the GitHub URL in protocol.md. I know that this is missing the URL and we are not adding that to the protocol until part F.

2. Claude says Step 3 quantification with Salmon in protocol.md was hardcoded to run as a single sample (S63). The way I wrote it that is true. I was unsure if the instructions said we were supposed to make a loop and I did try to write a loop but couldn't figure out how to pull the right files with a glob. 

3. Claude said there is no version pinning for TrimGalore, Salmon or MultiQC in the scripts. I think this is incorrect as all the scripts have the apptainer  that pins to a specific versio (e.g. salmon:2.8.0). Maybe Claude did not adequately review the scripts. 

### 18: Address a suggestion

*Answer*: Which suggestion(s) you addressed, and what you changed: I changed the protocol.md Step 3 quantification with Salmon to write `S<#>` instead of a specific number to indicate that that sample number changes depending on the desired sample.

------------------------------------------------------------------------

## Part F: Publish your repo on GitHub

### 19: Create a GitHub repository and push

<!-- No answer needed here -- we check your GitHub repo directly. -->

### 20: Get a (test) DOI with Zenodo

*Answer*: Your Zenodo Sandbox DOI: 10.5072/zenodo.636759

