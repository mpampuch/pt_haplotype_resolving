# Resolving Pt Haplotypes from ONT reads
This repository will be used to host all the code, notes, and workflow from the attempt to resolve haplotypes and find variants from Dan Gigueres ONT data of Pt. 
Make sure you follow the git collaborative workflow practices outlined in https://github.com/mpampuch/git_best_practices before contributing to this repository
Please make sure to add thorough comments, cleaned up code, and *timestamp* your notes

## Important resources
Please go over the PEPPER-Margin-DeepVariant manuscript and instructions as it's the pipeline we'll be using for this project
- https://www.nature.com/articles/s41592-021-01299-w
- https://github.com/kishwarshafin/pepper#quickstarts-small-runs-to-test-system-configuration

Also familiarize yourself with Dan Giguere's outputted data
- The individual chromosome sequences are available on the European Nucletoide Archive under the Project ID: PRJEB42700
	- https://www.ebi.ac.uk/ena/browser/view/PRJEB42700?show=reads
- The entire genome sequence can be found in a single file on one of his github repositories
	- https://github.com/dgiguer/phaeodactylum-tricornutum-genome

Also important to note is that the PEPPER-Margin-DeepVariant instructions provide instructions for using Docker. I don't know how to use Docker yet so it's one thing we should learn how to use first.

# Collaborative notebook

### Jan 26th - MP: 

Created a directory in the ubdata drive to attempt this
```
/Volumes/ubdata/mpampuch/pt_haplos_ont
```

Organized it like this as per the PEPPER github instructions (misc folder not part of the instructions just added by me)

```
(base) mpampuch@agrajag:~/pt_haplos_ont$ ls
input  misc  output  README.md
```

Cloned Dans repo into the misc folder

```
git clone https://github.com/dgiguer/phaeodactylum-tricornutum-genome.git pt_genome
mv pt_genome misc
```

### Feb 7th - MP:

Dan re-basecalled the reads over the weekend using Bonito. Shared them here:
- https://uwoca-my.sharepoint.com/:u:/g/personal/dgiguer_uwo_ca/Ece0oqtqtzVFnmtvvqBM28sBt7VHHkx9YbL8bkRibt2boA?e=1zqnWJ

I made a new directory inside misc and downloaded them there
- I learned that for files on sharepoint, you can download them directly using `wget` if in the URL you replace whatever is after the `?` with `download=1`
	
```
mkdir rebasecalled_reads

cd rebasecalled_reads

wget https://uwoca-my.sharepoint.com/:u:/g/personal/dgiguer_uwo_ca/Ece0oqtqtzVFnmtvvqBM28sBt7VHHkx9YbL8bkRibt2boA?download=1 --output-document=bonito_basecalls.fastq.gz

gunzip bonito_basecalls.fastq.gz
```

Mapped bonito reads to Dans genome assembly
```
minimap2 -ax map-ont -t 8 /Volumes/ubdata/mpampuch/pt_haplos_ont/misc/dans_pt_genome/data/final_pt_april_30_final.fasta /Volumes/ubdata/mpampuch/pt_haplos_ont/misc/rebasecalled_reads/bonito_basecalls.fastq | samtools sort -@4 -m 4G > bonito_reads_mapped.bam &
```

Moved mapped reads to new directory 
```
/Volumes/ubdata/mpampuch/pt_haplos_ont/misc/reads_mapped/bonito_reads_mapped.bam
```

Re-zipped the basecalls file
```
gzip --verbose bonito_basecalls.fastq 
```

## Feb 14th - MP

Last week we couldn't get the pipeline working on the Agrajag because we were running into permission issues.

Dan finished running PEPPER-Margin-DeepVariant over AWS on the Bonito re-basecalled reads. Shared them here.
- https://uwoca-my.sharepoint.com/:u:/g/personal/dgiguer_uwo_ca/EaROg6lco0hCh9cl5LLUNDgBxrobtjllYN7z3lwgDJVtLA?e=la2d8N

Extracted the files
```
mkdir pepper_output
cd pepper_output # path is /Volumes/ubdata/mpampuch/pt_haplos_ont/pepper_output
wget https://uwoca-my.sharepoint.com/:u:/g/personal/dgiguer_uwo_ca/EaROg6lco0hCh9cl5LLUNDgBxrobtjllYN7z3lwgDJVtLA?download=1
```

Running `file` on this downloaded file outputted this `Zip archive data, at least v2.0 to extract`

Needed to download `p7zip` to download this
- Did this through conda to my virtual environment `mark`

```
conda activate mark
conda install -c bioconda p7zip
7e e EaROg6lco0hCh9cl5LLUNDgBxrobtjllYN7z3lwgDJVtLA\?download\=1 #this was the zip file because I didn't rename it
```



