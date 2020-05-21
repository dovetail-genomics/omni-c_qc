# Omni-C QC
## Description

This repo hosts a shell script (`omni-c_qc.bash`), that can be used to perform quick QC on shallowly sequenced Omni-C libraries. It is also appropriate for DpnII Hi-C libraries. 

## Requirements

This script depends on the following tools:

- [BWA](https://github.com/lh3/bwa)
- [samtools](https://github.com/samtools)
- [samblaster](https://github.com/GregoryFaust/samblaster)
- [preseq](http://smithlabresearch.org/software/preseq/)

All of these tools are installable via [BioConda](https://bioconda.github.io). 

Install them however is most convinent for you. They are expected to be in your path.

If you want a prebuilt conda environment with all the dependencies required to run scripts in this repository, you can run following from the `conda` folder in this repo.

```
./create.sh
conda activate dovetail
```

This will create and activte an environment named `dovetail`. Once you are in this environment, you can run all the scripts here without installing any more dependencies!

## Running
Given paired FASTQ's and a reference FASTA, run:

```
./omni-c_qc.bash reference.fasta read1.fastq.gz read2.fastq.gz alignment.bam READGROUP_NAME
```

Substitute appropriate file names. READGROUP name is arbitrary.

## Output

After the script completes, it will print:

```
Mapping Quality Threshold         : 40
Read1                             : 4505616
Read2                             : 4440232
Mapped pairs                      : 3915274
PCR dupe pairs                    : 9847
Mapped nondupe pairs              : 3905427
Valid Pairs (cis>1000bp + trans)  : 3517577
Mapped nondupe pairs cis          : 3289426
Mapped nondupe pairs cis <=1000bp : 387850
Mapped nondupe pairs cis >1000bp  : 2901576
Mapped nondupe pairs cis >10000bp : 2157962
Mapped nondupe trans pairs        : 616001
Expected unique pairs at 300M sequencing:  269101956.2
```

We consider a library prepared from a **mammalian** sample to be acceptable if:
- Mapped nondupe pairs cis > 1,000 bp is greater than 20% of the total mapped nondupe pairs.
- Expected unique pairs at 300M sequencing is at least ~ 120 million.
 
We consider a library prepared from **non-mammalian** sample to be acceptable if:
- Mapped nondupe pairs cis < 1,000 bp is lower than 40% of the total mapped nondupe pairs.
- Expected unique pairs at 300M sequencing is at least ~ 120 million.

# Contact Map Creation
## Description
This is the description of the scripts that will enable creation of contact map in hic and cool format from the BAM file generated in QC step. 

## Requirements

This script depends on the following tools in addition to the tools required for the alignment QC:

- [pysam](https://pysam.readthedocs.io/en/latest/)
- [Cooler](https://cooler.readthedocs.io/en/latest/index.html)
- [hic2cool](https://github.com/4dn-dcic/hic2cool)
- [Juicer Tools](https://github.com/aidenlab/juicer)

All the tools except Juicertools are available in Bioconda. You can download the JAR file for juicertools and place it in the same directory as this reposity and name it as `juicertools.jar`. 

## Running
After you generate the BAM, you can run contact map script as:

```
./contact_map.sh alignment.bam reference.fasta Sample
```

This will generate 3 files: `Sample.hic` - Hi-C contact map in .hic format, `Sample.cool` - Hi-C contact map at 1kb resolution in cool format, and `Sample.mcool` - Hi-C contact map at multiple resolutions in mcool format. 
