## RepeatModeler

/path/RepeatModeler-2.0.3/BuildDatabase -name A1 A1_genome.fa
/path/RepeatModeler-2.0.3/RepeatModeler -database A1 -pa 20 -LTRStruct
/path/RepeatMasker/RepeatMasker A1_genome.fa -lib A1-families.fa -e rmblast -xsmall -s -gff -pa 12


## TE region analysis

grep -v Simple $genome.fasta.out | grep -v rRNA | grep -v Low > $TE.out
perl ./TE/rmskToBed.pl $genome.fasta.out | grep -v Simple | grep -v rRNA | grep -v Low > $TE.bed


## Braker

hisat2-build genome.fna genome_index
hisat2 -p 8 genome_index -1 RNA1.fastq -2 RNA2.fastq -S genomeRNAseq.sam

samtools view -bS genomeRNAseq.sam > genomeRNAseq.bam
samtools sort genomeRNAseq.bam -o genomeRNAseqsorted.bam
samtools index genomeRNAseqsorted.bam

braker.pl --species=beauveria_bassiana --genome=masked.fasta \
--bam=genomeRNAseqsorted.bam \
--TSEBRA_PATH=./TSEBRA/bin \
--GENEMARK_PATH=./gmes_linux_64 \
--gff3 --fungus --threads 16 --softmasking --min_contig=1000


## dbCAN

conda activate dbcan

dbcan_build --cpus 8 --db-dir db --clean

run_dbcan $braker.aa protein --out_dir $OUT_DIR
