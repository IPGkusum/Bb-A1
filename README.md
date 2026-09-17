# Assembly

## Short reads
java -jar /trimmomatics/Trimmomatic-0.39/trimmomatic-0.39.jar PE -phred33 -trimlog seq.log -threads 4 R1_001.fastq.gz R2_001.fastq.gz 1.fastq.gz 1.unpaired.fastq.gz 2.fastq.gz 2.unpaired.fastq.gz ILLUMINACLIP:~/trimmomatics/Trimmomatic-0.39/adapters/TruSeq3-PE.fa:2:30:10 SLIDINGWINDOW:5:20 LEADING:5 TRAILING:5 MINLEN:50

## long reads
dorado basecaller hac ./pod5_skip/ --kit-name SQK-NBD114-24 --no-trim > BbA1.bam
dorado demux --output-dir ./barcodes --no-classify BbA1.bam
samtools fastq ./barcodes/bcc8524081c9397a7903fb3a83ad03b12a11a9cd_SQK-NBD114-24_barcode01.bam > BbA1_barcodes01.fastq
porechop -i BbA1_barcodes01.fastq -o BbA1_barcodes01_trimmed.fastq

## MaSuRCA long + short reads
/MaSuRCA-4.1.0/bin/masurca -t 32 -i 1.fastq.gz,2.fastq.gz -r BbA1_barcodes01_trimmed.fastq

## BUSCO
busco -i ~/Masurca.fasta -l fungi_odb10 -o BbA1busco -m genome
