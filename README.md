# 1. BACASS Assembly

## Questions:

* How do we organise sequencing data?
    * Illumina reads have labels for each read (e.g. _1 and _2)
    * Do we store them in the same directory with standard labels?
    * Does each unit create their own script to create the samplesheet.csv?
* Do we provide a sample ID list?
    * We could get all genomes in directory with long and short reads
* Do we provide GenomeSize or basecalling model ?

## Install BACASS

TBD

## Prepare SampleSheet

Sample sheet required for the pipeline `samplesheet.csv` for hybrid assembly


| ID        | R1                            | R2                            | LongFastQ                   | Fast5 | GenomeSize |
|-----------|-------------------------------|-------------------------------|-----------------------------|-------|------------|
| G18252286 | /path/to/G18252286_1.fastq.gz | /path/to/G18252286_2.fastq.gz | /path/to/G18252286.fastq.gz | NA    | 2.8m       |
| G18252287 | /path/to/G18252287_1.fastq.gz | /path/to/G18252287_2.fastq.gz | /path/to/G18252287.fastq.gz | NA    | 2.8m       |



## Run BACASS

```
GENUS=Staphylococcus Aureus

nextflow run nf-core/bacass --input samplesheet.csv -profile docker -resume  --outdir hybrid_assembly_out --assembly_type hybrid --prokka_args `--genus ${GENUS}` --kraken2db "https://genome-idx.s3.amazonaws.com/kraken/k2_standard_8gb_20210517.tar.gz"
```


# 2. Dragonflye Assembly


## Install DragonFlye

Install the Dragonflye using the command and activate the environment:

```
conda create -n dragonflye -c conda-forge -c bioconda dragonflye
conda activate dragonflye
```


## Run DragonFlye

### Running dragonflye individually

```
dragonflye  --gsize 2.8M --R1  /path/to/G18252286_1.fastq.gz  --R2 /path/to/G18252286_2.fastq.gz  --reads /path/to/G18252287.fastq.gz --cpus 32 --ram 128 --prefix G18252287 --medaka 1 --model r941_min_sup_g507 --outdir dragonflye_out/G18252287 --force
```



Change the --model to as per the guppy base-calling performed.




### Running the dragonflye in batch mode using the bash script


Clone the repository containing the bash script to run the dragonflye
git clone https://github.com/varunshamanna/dragonflye_bash_pipeline.git


Edit the dragonflye_pipeline.sh to change the genome size and other default parameters.


The script takes 4 inputs from the user to run the dragonflye assembler: 
-l a folder containing nanopore reads
-s a folder containing short reads
-i a text file having ids for which the assembly has to be performed
-o output folder name


bash dragonflye_pipeline.sh -l long_fastqs/ -s short_fastqs/ -o dragonflye_out/ -i ids



The script will look for ID_R1.fastq.gz and ID_R2.fastq.gz files from the short_fastqs folder and ID.fastq.gz from the long_reads folder for each ID in the ids file provided and run dragonflye.
