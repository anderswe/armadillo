[![DOI](https://img.shields.io/badge/DOI-10.1038%2Fs41525--022--00292--2-green)](https://doi.org/10.1038/s41525-022-00292-2)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.5846705.svg)](https://doi.org/10.5281/zenodo.5846705)

# Armadillo

Mutations in repetitive regions are usually lost by standard variant callers as low mapping quality leads to non-reliable variant calls. We introduce Armadillo, a pipeline that stacks all copies from repetitive genes to make mutations in such regions visible. 

## Getting Started

### Prerequisites

* [BWA](http://bio-bwa.sourceforge.net/) (built with v.0.7.17)
* [Samtools](http://www.htslib.org/doc/samtools.html) (built with v.1.9)
* [gfClient](https://genome.ucsc.edu/goldenPath/help/blatSpec.html#gfClientUsage)  (built with v.35)
* [Python3](https://www.python.org) (built with v.3.7)


### Installation

Use git to download Armadillo:

```
git clone https://github.com/pbousquets/armadillo
```

Check that the dependencies are installed:

```
cd armadillo

./configure #run it as sudo to install the python packages and creating an alias globally 
```

## Getting armadillo ready

### 1. Open a port for blat 
Before we can run armadillo, we need to get a port prepared to run gfClient. In order to do that, we just run:

```
nohup gfServer start localhost PORT /path/to/reference_genome.2bit >/dev/null 2>&1 &
```
The port will stay opened unless we kill the task or shut the computer down.

### 2. Get the custom armadillo_data for your ROIs
The regions of interest must be analysed before running armadillo to keep just those which are repetitive and get the coords of their copies. Armadillo can do that just by providing a reference genome, a BED-formatted list of regions of interest and the port previously opened for gfClient:

```
armadillo data-prep -i /path/to/rois.bed -g /path/to/reference_genome.fa -p $PORT [-m min_len] [-o output_dir]
```

## Running armadillo

It's possible to use a configuration file to run armadillo. Just by using config-file option a configuration file will be copied at current working directory. Then, just change any parameter you want and run armadillo.

```
armadillo config-file
armadillo run configuration_file.txt
```
Options can be also passed directly through the command line:

```
armadillo run -n CASE -C control.bam -T tumor.bam --armadillo_data /path/to/armadillo_data [options]
```
__If using the dockerized version:__

We recommend adding "--net='host' -v /path/to/reference_genome.2bit:/path/to/reference_genome.2bit". This will allow to properly perform the queries even within the container. The complete command we recommend is:

```
docker run --rm -it --net='host' -v /path/to/reference_genome.2bit:/path/to/reference_genome.2bit -v /path/to/genomes:/path/to/genomes -v /home:/path/to/output armadillo [arguments]
```

### Output

The program will print multiple files:
- The stacked minibams of both tumor and normal samples, generated in the first step of the pipeline and used for variant calling. 
- CASE_candidates.vcf is the first one to be printed. It's an intermediate with mutations' readnames, which are then used by remove_dups.py to remove duplications. 
- CASE_final.vcf is the final VCF.  

## Authors

* **Pablo Bousquets Muñoz** - bousquetspablo@uniovi.es
* **Ander Díaz Navarro**
* **Xose Antón Suárez Puente**

## Citation

Bousquets-Muñoz, P., Díaz-Navarro, A., Nadeu, F. et al. PanCancer analysis of somatic mutations in repetitive regions reveals recurrent mutations in snRNA U2. npj Genom. Med. 7, 19 (2022). https://doi.org/10.1038/s41525-022-00292-2
