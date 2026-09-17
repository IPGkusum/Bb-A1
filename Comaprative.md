# Comparative genome

## Mummer
nucmer --maxmatch -c 100 -l 20 $Reference_genome $genome.fasta -p $PREFIX
show-coords -rcl $PREFIX.delta > $PREFIX.coords

awk '$19 && $4 && $5 {OFS="\t"; print $19, ($4 < $5 ? $4-1 : $5-1), ($4 < $5 ? $5 : $4)}' $PREFIX.coords | sort -k1,1 -k2,2n > $PREFIX_out.bed

seqkit fx2tab $genome.fasta -l -n > genome.SeqLen
awk 'OFS="\t"{print $1,0,$2}' genome.SeqLen > genome.bed

bedtools subtract -a $ genome.bed -b $PREFIX_out.bed > $ PREFIX_specific.bed
awk '{print $1,$2,$3,$3 - $2}' $PREFIX_specific.bed > $PREFIX _specific_min.txt
sort -k4 -n -r $PREFIX _specific_min.txt > $PREFIX _specific_min_sort.txt

## local blast
makeblastdb -in $Reference_genome.fasta -out $DATABASE -dbtype 'nucl'

blastn -task blastn -query $specific_region -db $DATABASE -outfmt "6 qacc sacc evalue bitscore length qcovs pident" -out $OUT

sort -k6 -n $OUT > $OUT_sort.txt

awk '{if($6<=20) print $0}’ $OUT_sort.txt > $OUT20.txt

awk '{print $1}' $OUT20.txt | uniq > $OUT_uniq.txt

## Common uniq region
for file in $OUT_uniq.txt; do
  awk -F'[:-]' '{print $1 "\t" $2-1 "\t" $3}' $file > ${file%.txt}.bed
done

for file in uniq*.bed; do
  bedtools sort -i $file | bedtools merge -i - > ${file%.bed}_merged.bed
done

bedtools multiinter -i ${file%.bed}_merged.bed > common_regions.bed

awk '$4 == 5' common_regions.bed > A1_common_regions.bed

awk '{print $1 ":" $2+1 "-" $3}' 157final_common_regions.bed > A1_common_regions.txt

