# Testing hybrid assembly tools


## 0. Getting the data:

For these tests, you will need to get the data from a digital ocean server.

You can access the server using the following credentials (I will share the password via slack):

```
username: angela_col
password: shared in slack
host:104.248.164.189 
path: /media/storage
```

For example, you may be able to retrieve the data from the server using `scp`

```
scp -r angela_col@104.248.164.189:/media/storage .
```

The files are labeled as:

```
{UNIT}-{SPECIES}-{SAMPLE_ID}-{TECHNOLOGY}{-R1 or R2 for illumina}.fastq.gz
```


## A. BACASS Assembly

### 1. Install BACASS

#### Nextflow

Install as insctructed in [www.nextflow.io](https://www.nextflow.io/docs/latest/getstarted.html)

#### nf-core

Check [https://nf-co.re](https://nf-co.re/docs/usage/tutorials/nf_core_usage_tutorial#installing-the-nf-core-helper-tools) for any additional help.

### 2. Prepare SampleSheet

Sample sheet required for the pipeline `samplesheet.csv` for hybrid assembly


| ID  | R1                        | R2                        | LongFastQ               | Fast5 | GenomeSize |
|-----|---------------------------|---------------------------|-------------------------|-------|------------|
| IDA | `/path/to/IDA_1.fastq.gz` | `/path/to/IDA_2.fastq.gz` | `/path/to/IDA.fastq.gz` | NA    | 2.8m       |
| IDB | `/path/to/IDB_1.fastq.gz` | `/path/to/IDB_2.fastq.gz` | `/path/to/IDB.fastq.gz` | NA    | 2.8m       |



### 3. Run BACASS

```
GENUS=Staphylococcus Aureus

nextflow run nf-core/bacass --input samplesheet.csv -profile docker -resume  --outdir hybrid_assembly_out --assembly_type hybrid --prokka_args `--genus ${GENUS}` --kraken2db "https://genome-idx.s3.amazonaws.com/kraken/k2_standard_8gb_20210517.tar.gz"
```


## B. Dragonflye Assembly


### 1. Install DragonFlye

Install the Dragonflye following [the official repo](https://github.com/rpetit3/dragonflye):

I suggest using [mamba](https://mamba.readthedocs.io/en/latest/).

```
mamba create -n dragonflye -c conda-forge -c bioconda dragonflye
conda activate dragonflxye
```


### 2. Run DragonFlye

#### Running dragonflye individually

```
dragonflye  --gsize 2.8M --R1  /path/to/G18252286_1.fastq.gz  --R2 /path/to/G18252286_2.fastq.gz  --reads /path/to/G18252287.fastq.gz --cpus 32 --ram 128 --prefix G18252287 --medaka 1 --model r941_min_sup_g507 --outdir dragonflye_out/G18252287 --force
```

Change the --model to as per the guppy base-calling performed.


#### Running the dragonflye in batch mode


Clone the repository containing the bash script to run dragonflye in batch


```bash
git clone https://github.com/varunshamanna/dragonflye_bash_pipeline.git
```

> Edit the dragonflye_pipeline.sh to change the genome size and other default parameters.


```bash
bash dragonflye_pipeline.sh -l long_fastqs/ -s short_fastqs/ -o dragonflye_out/ -i ids
```


## C. Metrics to Report

Please report your output metrics by modifying [report.csv](https://github.com/cgps-group/hybrid_assembly_test/blob/main/report.csv) and creating a pull request.

* Num. of circulirised contigs
* N50
* GC % (chromosome / plasmid)?
* Taxonomy
* ST 
* Read Length / Read Coverage

##### Initially developed by Varun Shamana and Julio Diaz Caballero
