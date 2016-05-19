## SQUID: Stringent Quantitation of Unspliced Intron by Deep-sequencing

Requirements
------------
1. Install Python 2.7.x and corresponding versions of NumPy and
SciPy.
2. Install pysam version 0.8.4
3. Add the Python directory to the $PATH environment variable.

Installation:
------------
The source code can be directly called from Python.

Usage:
--------------------------------
	python SQUID.py --GTF ./test.gtf -i ./test_R1.bam,./test_R2.bam,./control_R1.bam,./control_R2.bam --anchor 8 --length 100 --lib first --read P --Cal All --RPKM gene.txt --norm 33324688,31783182,33779359,40647996 --Clean true --c1 0.05 --c2 0.05 --c3 0.05 --p 1 --Comparison ./Comparison --analysis U -o ./bam_first

	python SQUID.py --GTF ./test.gtf -i ./test_R1.sam,./test_R2.sam,./control_R1.sam,./control_R2.sam --anchor 8 --length 100 --lib first --read P --Cal All --RPKM gene.txt --norm 33324688,31783182,33779359,40647996 --Clean true --c1 0.05 --c2 0.05 --c3 0.05 --p 1 --Comparison ./Comparison --analysis U -o ./sam_first

Status API Training Shop Blog About

Required Parameters:
------------
	-i/--input:
		s1.bam/s1.sam[,s2.bam/s2.sam]. Mapping results for all of samples in bam/sam format. Different samples  are sepreated by commas
	--GTF:
		The gtf file
Optional Parameters:
------------	
	--o/--output:
		The output directory. The default is current directory
	--lib:
		The library type with choices of unstrand/first/second. The details are explained in the parameter of library-type in tophat2. The default is unstrand
	
	--read: 
		The sequencing strategy of producing reads with choices of P/S. The default is P
	--length: 
		The read length in nucleotide. The default length is 100
	--anchor: 
		The anchor length in nucleotide. The program will only count reads spanning junctions with at least this anchor length on each side. The default is 8
	--Cal: 
		Which  part of the program user choose to run, the choices are All/count/rMATS. All means run the whole program, count means only run the PI value calculation part, rMATS means only run the differential analysis of retained intron.  The default is All
	--RPKM: 
		A file providing the RPKM value for each sample, the first column is gene ID with the following column being the RPKM value for each sample. It is a required parameters to run the Density calculation
	--norm: 
		Total uniquely mapped reads for each library,each sample is seperated by comma, it is required to run the Density calculation
	--Clean: 
		true/false, whether to carry out PI_Density' calculation,The default is true
	--lim: 
		The minimum average number read per sample of the splice junction to be used in adjusting introns. The default value is 2
	--Comparison: 
		A file providing the sample pairs to calculate the differential RI level.The format should be column 1(name of comparions), column 2 (sample 1 order in the input file,replicates seperated by commas), column 3 (sample 2 order in the input file,replicates seperated by commas),column 4(type of PI value use to perform rMATS), column 5 (optional, if present as 'pool', the replicates are combined together). If absent, rMATS step will be skipped
	--analysis: 
		Type of rMATS analysis to perform. analysisType is either P or U. P is for paired analysis and U is for unpaired analysis. Default is U
	--c1: 
		The cutoff of splicing difference using Junction method. The cutoff used in the null hypothesis test for differential splicing. The default is 0.0001
	--c2: 
		The cutoff of splicing difference using Density method. The cutoff used in the null hypothesis test for differential splicing. The default is 0.0001
	--c3: 
		The cutoff of splicing difference using Density' method. The cutoff used in the null hypothesis test for differential splicing. The default is 0.0001
	--p: 
		The number of threads used to run rMATS. The default is 1;

Type of PI (Percent of Introns) Calculation:
------------	
	PI:
		Unspliced counts divided by transribed counts
	PI_Junction: 
		Inclusion counts divided by the sum of inclusion and  skipping junction counts
	PI_Density:
		The observed counts divided by the expected counts of the intron
	PI_Density': 
		The observed counts divided by the expected counts of the adjusted intron

Output list:
------------
	result:
		All of final result files are in result folder.
	counts_all_$type.txt store the inclusion and skipping counts for all of the samples
		column 1: Intron Id representing the chromosome position, start and end.
		column 2: Gene id
		column 3: Strand
		column 4: Chromosome name
		column 5: Start coordinate
		column 6: End coordinate
		column 7: Whether this intron was annotated in the gtf file as retained intron event.
		column 8: Whether this intron was overlapped with exon, or the 5' splice site was overlapped with exon or the 3' site was overlapped with exon or whether this intron is a simple intron
		column 9: Unspliced counts for all of the samples seperated by commas
		column 10: Transcribed counts for all of the samples seperated by commas
		column 11: Unspliced  length
		column 12: Transcribed length
		column 13: PI value for all of the samples seperated by commas
	rMATS_Result_$comparison_$type.txt store the differential RI level calculated by rMATS
		column 1: Intron Id representing the chromosome position, start and end.
		column 2: Gene id
		column 3: Strand
		column 4: Chromosome name
		column 5: Start coordinate
		column 6: End coordinate
		column 7: Whether this intron was annotated in the gtf file as retained intron event.
		column 8: Whether this intron was overlapped with exon, or the 5' splice site was overlapped with exon or the 3' site was overlapped with exon or whether this intron is a simple intron
		column 9: Unspliced counts for all replicates  of sample 1 seperated by commas
		column 10: Transcribed  counts for all replicates  of sample 1 seperated by commas
		column 11: Unspliced counts for all replicates  of sample 2 seperated by commas
		column 12: Transcribed counts for all replicates  of sample 2 seperated by commas
		column 13: Unspliced length
		column 14: Transcribed length
		column 15: p-value for differential PI level of the two samples
		column 16: FDR for differential RI level of the two samples
		column 17: PI level for sample1, replicates seperated by commas
		column 18: PI level for sample2, replicates seperated by commas
		column 19: The difference of PI level between sample1 and sample2, which is the result of average PI level of sample1 minus the average PI level of sampel2.

	test:
		A folder contains test files to run the program

	log.SQUID: Log file for running CARIE pipeline

	gtf_files:
		A folder contains different types of gtf files to run the program. Use mouse genome as examples.
	Mus_musculus.Ensembl.GRCm38.78.gtf: the ensemble gtf files. This file should be provided by user. 
	Exon_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains exons only
	Intron_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains intron only
	Intron_Annotated_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains the attributes whether the intron was annotated as retended introns in the original gtf files
	Intron_clean_Mus_musculus.Ensembl.GRCm38.78.gtf: the gtf file contains the attributes whether the intron/5'Junction/3'Junction was overlapped with Exon and whether the intron is a simple intron. 

	counts:
		A folder contains all of the count files
	n = number of samples
	count_all.txt: a file contains the counts for all of the introns
		column 1:Intron Id representing the chromosome position, start and end.
		column 2:Gene id
		column 3:Strand
		column 4:Chromosome name
		column 5:Start coordinate
		column 6:End coordinate    column 1: Intron Id representing the chromosome position, start and end.
		column 7: Inclusion counts at 5' splice sites for sample 1
		column 8: Skipping counts at 5' splice sites for sample 1
		column 9: Inclusion counts at 3' splice sites for sample 1
		column 10: Skipping counts at 3' splice sites for sample 1
		column 11: Skipping counts of the intron for sample 1
		column 12: counts lying in the intron for sample 1
		column 13-6*(n+1): more counts for samples 2-n
	count_all_Density.txt: a file contains the observed counts and expected counts for all of the
	
	rMATS_files:
		A folder contains all of the rMATS input and output files
	rMATS_$comparison_$type.txt
    		The input file for running rMATS.
	rMATS_$comparison_$type folder
		The folder contains the result of rMATS output.



