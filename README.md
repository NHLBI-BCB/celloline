# Celloline: A pipeline for mapping and quality assessment single cell RNA-seq data

`Celloline` is a pipeline that handles parallel mapping and quantification of single cell RNA-seq, using standard mapping tools and quantifiers. 

# Availability
The pipeline is available at: [gitHub](https://github.com/ti243/celloline) and as an machine image on Amazon, where 3rd party tools and programs have been preinstalled. This image can be used on the Amazon cloud or exported for several platforms of virtual machines [See later sections](#Running the celluline pipeline on the pre-installed Amazon machine image AMI).

# Key features
* `Celloline` maps and quantify data from single cell RNA-seq experiments in parallel.
* `Celloline` allows for quick re-running the mapping, statistics collection and quantification on new experiments, with a change of one parameter.
* `Celloline` allows for quck testing optimal mapper and quantification tool for your experiment, and out puts your results in a uniform fasion.

### See usage and parameters by typing:
python pipeline.py -h 

### What you need to run the program:
* Directory of single cell sequencing data (parameter -i)
* Mapping tool (parameter -m) - at least one of: bowtie, bowtie2, STAR, BWA, gsnap, salmon
* Reference genome - either an index that fits the mapping tool (parameter -g) or as fasta and gtf (use -f and -gtf)
* Quantification tool - at least one of: Tophat (run with bowtie or bowtie2), htseq-count or cufflinks 
* Ulitity tools: samtools, gtf_splicesites, iit_store, picard-tools, bam2fastq, iit_store


<!--- list is to be continued - provide links to 3rd party tools -->

## `celloline` workflow

The diagram below provides an overview of the functionallity of `celloline` and its partner tool `cellity`(https://github.com/ti243/cellity).

![Diagram outlining the celloline workflow](inst/cellity_overview.png)


# Getting started
The pipeline can be run from our Amazon instance or by installing the the pipeline and 3rd party tools from 
For the most simple running of the pipeline create a directory (eg. /data1/scRNAdata/MouseKO/) for running of the pipeline. Store fastq files in a directory called "raw" (/data1/scRNAdata/MouseKO/raw). Store a fasta file and a gtf files of the reference gennome (e.g. /data1/reference_genomes/mm9.fasta, /data1/reference_genomes/mm9.gtf). Set appropriate paths in the config file (see config_example.txt)

### Naming of files
From the provided data path (input dir relative to provided root - see config file) the pipeline expects a directory named "raw" containing all the files in the experiment.
Files should be named with a hash and a number, that identifies the cell. e.g. for cell number 45. 
SingleCell/#45_1.fq
The two latter "_1" and ".fq" are identifiers for paired read number and fastq-file extention, respectively; and can be set in the config file. Some lines of code as inspiration for renaming or linking to your files can be found in [here](lib/prepare_filename_pointers.sh) 




## The config file explained
Please refer to the config_example.txt in order to understand the structure. 

[DIRECTORIES] | Help
--- | ---
ROOT_DIR={path}                    | *prefix for input directory. Could be a single cell project directory. Following fastq files must be in {ROOT_DIR}/{INPUT DIRECTORY}/raw/\*
TEMP_DIR={path}                    | *all temporary files will be stored here - could be a fast non-backed up drive *
REF_DIR={path}                     | *path to reference genomes should contain directories named according to /{species_name}/{mapper_name} (-g and -m parameters). this can be build uding the. The reference genomes can be build by the pipeline, just requires setting -f parameter*
TOOLS_MAPPING =                    | *path to mapping tools - will be added to path and must thus contain executables in child directories*
TOOLS_QUANTIFICATION = {path}      | *prefix for quantification tools.*
MAPPING_ROOT = mapped              | *prefix for mapping tools*
QUANTIFICATION_ROOT = counts       | *directory name for count data*
PREPROCESSED_ROOT = preprocessed   | *directory name for preprocessed files*
SORTED_BAM_ROOT = sorted_bam       | *directory name for sorted bam files.*
SAM_ROOT = sam                     | *directory name for samfiles*
										
[EXTENSIONS] | Help	
--- | ---							
EXT_BAM = .bam						| 	*bam file extention*
EXT_FASTQ= .fq						|	*fastq file extention*
EXT_FASTQ_GZ = .fq.gz				|	*compressed fastq file extention*	
EXT_SAM = .sam						|	*sam file extention*
EXT_SORTED = .sorted				|	*sorted file extention*
EXT_COUNTS = .counts				|	*count file extention*
EXT_METRICS = .metrics				|	*metrics file extention*
EXT_MERGED = .merged				|	*merge file extention*
EXT_MARKED_DUPL= .marked.duplicates	|	*dublicate file extention*
EXT_FASTA = .fa						|	*FASTA file extention*
EXT_LOG = .log						|	*log file extention*
EXT_GTF= .gtf						|	*gtf file extention*
EXT_GSNAP_SPLICE = .splice_sites	|	*Gsnap splice file extention*
EXT_GSNAP_IIT = .iit				|	*GSNAP iit file extention*
EXT_DICT = .dict					|	*genome dictionary file extention*
EXT_SUMMARY = .stat                 |	*Mapping statistics file extention*
FORWARD_STRAND = _1                | *Identifier used if analysing paired end reads.*
REVERSE_STRAND = _2                 | *Identifier used if analysing paired end reads.*


[SOFTWARE] | Help
--- | ---
GSNAP=/gsnap 	| *give path relative to MAPPING_ROOT*
GSNAP_BUILD=/gmap_build 	| *give path relative to MAPPING_ROOT*
GTF_SPLICE_TOOL = /gtf_splicesites	| *give path relative to MAPPING_ROOT*
GTF_IIT_TOOL = /iit_store	| *give path relative to MAPPING_ROOT*
BOWTIE1_BUILD=/bowtie-build	| *give path relative to MAPPING_ROOT*
BOWTIE1=/bowtie	| *give path relative to MAPPING_ROOT*
BOWTIE2_BUILD=/bowtie2-build	| *give path relative to MAPPING_ROOT*
BOWTIE2=/bowtie2	| *give path relative to MAPPING_ROOT*
BWA_BUILD=/bwa	| *give path relative to MAPPING_ROOT*
BWA=/bwa	| *give path relative to MAPPING_ROOT*
STAR_BUILD = /STAR	| *give path relative to MAPPING_ROOT*
STAR = /STAR	| *give path relative to MAPPING_ROOT*
TOPHAT = /tophat2	| *give path relative to MAPPING_ROOT*
SALMON_BUILD = /salmon	| *give path relative to MAPPING_ROOT*
SALMON = /salmon	| *give path relative to MAPPING_ROOT*
HTSEQTOOL=htseq-count    | *give path relative to QUANTIFICATION_ROOT*
CUFFLINKS = /cufflinks    | *give path relative to QUANTIFICATION_ROOT*
PICARD_TOOL = /home/ubuntu/bin/picard-tools-1.113     | *give full path*
BAM_2_FASTQ =/home/ubuntu/bin/bam2fastq-1.1.0    | *give full path*
SAMTOOLS = /usr/bin/samtools    | *give full path*



#Running the celluline pipeline on the pre-installed Amazon machine image AMI

In order to use the amazon instance you need to setup an AWS account [AMAZON AWS](https://aws.amazon.com). This requires a credit card number and a phone number for verification. There is a free trial for all new registrants, to try out the service, and institutions with teaching responsibilities might be eligible for further free credits. The AMI can also be exported as a VMware image for use on local clusters. [See Amazons guide](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ExportingEC2Instances.html). The cluster installation has been tested to work with gmap, bowtie2, and STAR, using quantifyers HTSeq and cufflinks.

The AWS instance handles jobs via [GNU parallel](http://www.gnu.org/software/parallel/), and this job handling (pipeline parameter --cluster aws) is also well suited for dedicated servers, instances or farms which are not dependent on LSF queueing systems using bsub. Please cite parallel appropriately, as asked on the website.

After creating a AWS profile go to the console and choose ES2 and "Launch Instance" and then Community AMIs, where you search for celloline.

##Running the pipeline with test data.
We advice that you always try the pipeline with a small toy set for testing that everything works. This could be a subset of the cells and only the first few thousand reads. This can ususlly be run on the cheap t2.large instance, and will make sure that everything runs before upgrading the instance.

To get started with a test run quickly, start by making an index-file for the mapper of your choice:
```
pipeline.py -m gmap -f /home/ubuntu/references/Mouse/chr9.fa -gtf /home/ubuntu/references/Mouse/chr9.gtf -g chr9_fa -c /home/ubuntu/projects/example/config.txt -q htseq-count -i example --cluster aws --ram 10 -cpu 2
```
In the example we used only a small file with chromosome9. The full mouse genome is also preloaded in the same directory, but will take longer to run. Here we are providing fasta of the genome (-f) and gtf-file for the same genome (-gtf). Therefor the pipline will run only for creating a genome index - this only has to be done once for every mapper and genome. Hereafter the index-files can be retrieved by the pipeline.

Thereafter run the pipeline 
```
pipeline.py -m gmap -g chr9_fa -c /home/ubuntu/projects/example/config.txt -q htseq-count -i example --cluster aws --ram 10 -cpu 2
```

This is the same command without the genome fasta and gtf-file. Note that the genome will substitute /"./" with "_" so that chr9.fa becomes a genome called chr9_fa.

It means: The pipeline will use the mapper *gmap* with the geome called *chr9_fa* with the configuration file */home/ubuntu/projects/example/config.tx*, quantification tool *htseq-count* using the *example* project and the GNU parallel job-handler (*aws*) requiring 10 mb of RAM free before starting new jobs on the server and allows for using 2 cpus for each job. 

You will find your statistics, mapped files and counts in the specified temp directory.

## The AMI is structured as follows:
The configuration-file that you provide on runing the pipeline determine where the pipline will look for and also store files. It will be neccesary to attach more storage to the instance, when using full size files. For this purpose the setting of these directories in the top if th config is handy. Alternatively symbolic links can be used to achieve the same. In order to learn how to attach storage to the instance [EBS](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html)

###projects###
In the projects-directory is found and example project, and this is where new projects can be created. Each project directory should, when starting the pipeline, contain a directory called */raw* where uncompressed fastq-files are stored. These files should end with a *’#’* followed by the number of the cell, and an *”_”* followed by 1/2 indicating forward/reverse strand for paired end reads. Thus, in a paired read experiment cell number 84 would have two files that could be called
`singleCell#84_1.fastq and singleCell#84_2.fastq`

This can be comprised of a collection of symbolic links. Collecting and renaming of files depends on the naiming convention used by your sequencing facility, but some inspiration on some lines of code that could brung your about this task with numbering the cells quickly can be found in the (xxx), which must be altered to suit your filenames. We suggest not to rename the original files, in order to avoid loosing information or identity of the cells.
The project-directory also contains your **configuration file**. Please copy this file from the example. Please inspect this file, and set all the directories in the top of the file. 

###tools
Here are symbolic links the all the tools installed in the pipeline, to avoid a croveded bin.

###references
Here, the reference genomes are stored. On the AMI fasta and gtf-files for the mouse genome is stored along with smaller files only containing chomosome 9,for testing. Here the index-files for the mappers will also be stored.

###temp
This is the directory where temporary files are stored. This presently includes count-matices and statistics. This directory should be able to contain mapping and quantification output from all cells, and is better placed on a fast storage device with larger capacity (e.g. Amazon EBS), when running the pipeline on full size files. Many files in the temporary directory maybe deleted after run, for storage space considerations.

# Processing your own data on Amazon
We suggest to run a files each with a few milion reads though though the pipeline first to test using the smallest possible instance type (t2.large)
When using your own data in the pipeline you will need more storage and will have to attach an [EBS](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html). We suggest to use burstable instances (t2) for making index and copying files and then later upgrade the instance to a larger one or a [cluster](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/create_cluster.html) of multiple. Please be aware that all other instances than t2 are charged per hour when running, regardless of whether the CPUs are working or not.



