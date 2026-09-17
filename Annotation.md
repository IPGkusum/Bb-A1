# Annotation

## Repeat region analysis

```bash
/path/RepeatModeler-2.0.3/BuildDatabase -name A1 A1_genome.fa
/path/RepeatModeler-2.0.3/RepeatModeler -database A1 -pa 20 -LTRStruct 
/path/RepeatMasker/RepeatMasker A1_genome.fa -lib A1-families.fa  -e rmblast -xsmall -s -gff -pa 12

