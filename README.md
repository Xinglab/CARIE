## SQUID: Stringent Quantitation of Unspliced Intron by Deep-sequencing

Requirements
------------
1. Install Python 2.7.x and corresponding versions of NumPy and
SciPy.
2. Install cufflinks
3. Install kallisto
2. Install pysam version 0.8.4
3. Add the Python directory to the $PATH environment variable.

Installation
------------
The source code can be directly called from Python.

Usage
--------------------------------
Run SQUID with kallisto
	
	python ../SQUID.py --GTF ./test.gtf -i ./test_R1.bam,./test_R2.bam,./control_R1.bam,./control_R2.bam --fasta ./test_R1_1.fq:./test_R1_2.fq,./test_R2_1.fq:./test_R2_2.fq,./control_R1_1.fq:./control_R1_2.fq,./control_R2_1.fq:./control_R2_2.fq --index=./kallisto/test --anchor 8 --length 100 --lib unstrand --read P --Cal All  --c1 0.05  --p 1 --Comparison ./Comparison --analysis U -o ./bam
	
Run SQUID with cufflinks

	python ../SQUID.py --GTF ./test.gtf -i ./test_R1.sam,./test_R2.sam,./control_R1.sam,./control_R2.sam --anchor 8 --length 100 --lib first --read P --Cal All --RPKM gene_exp.txt --c1 0.05  --p 1 --Comparison ./Comparison --analysis U -o ./sam_first

Required Parameters
------------
	-i/--input:
		s1.bam/s1.sam[,s2.bam/s2.sam]. Mapping results for all of samples in bam/sam format. Different samples are sepreated by commas
	--GTF:
		The gtf file
		
Optional Parameters
------------	
	--o/--output:
		The output directory. The default is current directory
	--fasta: 
		s1_1.fq[:s1_2.fq][,s1_1.fq[:s2_2.fq],...]. The raw sequencing reads in fasta or fastq format that is required to call kallisto to calculate RPKM values, otherwise, cufflinks will be called
	--index:
		The path to the kallisto index that is required to run kallisto from raw reads. Without index provided, cufflinks will be called to calculate RPKM value
	--lib:
		The library type with choices of unstrand/first/second. The details are explained in the parameter of library-type in tophat2. The default is unstrand
	--read: 
		The sequencing strategy of producing reads with choices of P(paired end) or S (single end). The default is P
	--length: 
		The read length of sequencing reads in nucleotide. The default length is 100
	--anchor: 
		The anchor length in nucleotide. The program will only count reads spanning junctions with at least this anchor length on each side. The default is 8
	--Cal: 
		Which  part of the program user choose to run, the choices are All/count/DSI. All means run the whole program, count means only run the PI value calculation part, DSI means only run the differential analysis of spliced introns. The default is All
	--RPKM: 
		A file providing the RPKM value for each sample, the first column is transcript ID with the following column being the RPKM value for each sample. If it is not provided, kallisto or cufflinks will be called to calculate RPKM value
	--Comparison: 
		A file providing the sample pairs to calculate the differential RI level.The format should be column 1(name of comparions), column 2 (sample 1 order in the input file,replicates seperated by commas), column 3 (sample 2 order in the input file,replicates seperated by commas),column 4(type of PI value use to perform rMATS), column 5 (optional, if present as 'pool', the replicates are combined together for PI_Junction). If absent, calculation of differential spliced introns will be skipped
	--analysis: 
		Type of rMATS analysis to perform. analysisType is either P or U. P is for paired analysis and U is for unpaired analysis. Default is U
	--c1: 
		The cutoff of splicing difference using Junction method. The cutoff used in the null hypothesis test for differential splicing. The default is 0.0001
	--p: 
		The number of threads used to run rMATS. The default is 1;
	--resume:
		Whether to resume previous run. The default is false.

Types of PI (Percent of Introns) Calculation
------------	
	PI_Junction: 
		Inclusion counts divided by the sum of inclusion and  skipping junction counts
	PI_Density:
		The observed counts divided by the expected counts of the intron

Output list
------------
Notes

		n: number of samples
		$type: Types of PI including PI_Junction and PI_Density
		$comparison: The label of the each comparison provided in the Comparison file

###Result###
The folder contains all of final result files

counts_all_$type.txt store PI values for all of the samples

		column 1: Intron Id representing the chromosome position, start and end
		column 2: Gene id of intron residing genes
		column 3: Strand of intron residing genes
		column 4: Chromosome name of introns
		column 5: Start coordinate of introns
		column 6: End coordinate of introns
		column 7: Whether this intron was annotated in the gtf file as retained intron event
		column 8: Comma seperated logical values to denote Whether this intron was intron (E) or intron (M)
		column 9: Inclusion counts for all of the samples seperated by commas
		column 10: Skipping counts for all of the samples seperated by commas
		column 11: Effective inclusion length
		column 12: Effective skipping length
		column 13: PI value for all of the samples seperated by commas

rMATS_Result_$comparison_$type.txt store the result of differential PI for two samples

		column 1: Intron Id representing the chromosome position, start and end
		column 2: Gene id of intron residing genes
		column 3: Strand of intron residing genes
		column 4: Chromosome name of introns
		column 5: Start coordinate of introns
		column 6: End coordinate of introns
		column 7: Whether this intron was annotated in the gtf file as retained intron event
		column 8: Comma seperated logical values to denote Whether this intron was intron (E) or intron (M)
		column 9: Inclusion counts for all replicates  of sample 1 seperated by commas
		column 10: Skipping  counts for all replicates  of sample 1 seperated by commas
		column 11: Inclusion counts for all replicates  of sample 2 seperated by commas
		column 12: Skipping counts for all replicates  of sample 2 seperated by commas
		column 13: Effective inclusion length
		column 14: Effective skipping length
		column 15: p-value for differential PI level of the two samples
		column 16: FDR for differential RI level of the two samples
		column 17: PI level for sample1, replicates seperated by commas
		column 18: PI level for sample2, replicates seperated by commas
		column 19: The difference of PI level between sample1 and sample2, which is the result of average PI level of sample1 minus the average PI level of sampel2.

###test###
A folder contains test files to run the program

###log.SQUID###
 Log file for running SQUID pipeline

###gtf_files###
An intermediate folder contains different types of gtf files to run the program. Use mouse genome as examples.

	Mus_musculus.Ensembl.GRCm38.78.gtf: the ensemble gtf files. This file should be provided by user. 
	Exon_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains exons only
	Intron_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains intron only
	Intron_Annotated_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains the attributes whether the intron was annotated as retended introns in the original gtf files
	Intron_attri_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains the attributes whether the intron was overlapped with Exon and whether the intron is overlapped with other intron. 

###counts###
An intermediate folder contains all of the count files

count_all.txt: a file contains the counts for all of the introns		
	
		column 1:Intron Id representing the chromosome position, start and end.
		column 2:Gene id
		column 3:Strand
		column 4:Comma seperated logical values to denote Whether this intron was intron (E) or intron (M) based on read info
		column 5:Chromosome name
		column 6:Start coordinate
		column 7:End coordinate    
		column 8: Inclusion counts at 5' splice sites for sample 1
		column 9: Skipping counts at 5' splice sites for sample 1
		column 10: Inclusion counts at 3' splice sites for sample 1
		column 11: Skipping counts at 3' splice sites for sample 1
		column 12: Skipping counts of the intron for sample 1
		column 13: counts lying in the intron for sample 1
		column 14~6*(n+1)+1: more counts for samples 2-n
		
count_all_Density.txt: a file contains the observed counts and expected counts for all of the introns

		column 1: Intron id representing the chromosome position, start and end.
		column 2: The length of introns
		column 3~n+2: The observed counts
		column n+3~2n+2: The expected counts
		
Total.txt: a file contains total number of unique reads in each sample

		column 1~n: Total number of unique reads in sample 1~n

###RPKM		
An intermediate optional folder contains the result of RPKM result and gene expression files for the squid run without gene expression file provided. 

	kallisto_$n
		The result of kallisto of each sample
	cufflinks_$n
		The result of cufflinks of each sample
	transcript_exp.txt
		The file is RPKM file that contains RPKM value for each gene.
		column 1: Gene ID
		column 2~n+1: RPKM value for samples 
		
###rMATS_files###
An intermediate folder contains all of the rMATS input and output files

	rMATS_$comparison_$type.txt
    		The input file for running rMATS.
	rMATS_$comparison_$type folder
		The folder contains the result of rMATS output.
		
###DEXSeq###
An intermediate folder contains all of the rMATS input and output files

	rMATS_$comparison_$type.txt
    		The input file for running rMATS.
	rMATS_$comparison_$type folder
		The folder contains the result of rMATS output.


Contacts and bug reports
------------------------
Yi Xing
yxing@ucla.edu

Shaofang Li
sfli001@gmail.com

If you found a bug or mistake in this project, we would like to know about it.
Before you send us the bug report though, please check the following:

1. Are you using the latest version? The bug you found may already have been
   fixed.
2. Check that your input is in the correct format and you have selected the
   correct options.
3. Please reduce your input to the smallest possible size that still produces
   the bug; we will need your input data to reproduce the problem, and the
   smaller you can make it, the easier it will be.


Copyright and License Information
---------------------------------
Copyright (C) 2015 University of California, Los Angeles (UCLA)
Shaofang Li, Yi Xing

Authors: Shaofang Li, Yi Xing

This program is free software: you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation, either version 3 of the License, or (at your option) any later
version.

This program is distributed in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program. If not, see http://www.gnu.org/licenses/.

