#Trimmomatic command
```java
java -jar ~/Trimmomatic-0.36/trimmomatic-0.36.jar PE $FILEF $FILER paired_output_${FILEF%} \
unpaired_output_${FILEF%} paired_output_${FILER%} unpaired_output_${FILER%} -trimlog output.log \
ILLUMINACLIP:TruSeq3-PE-2.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```

# Deconseq script for removal of potential Human Contamination
 ```perl 
 perl deconseq-standalone-0.4.3/bwasw_modified_source/deconseq.pl \
    -f file.fq \
    -dbs hs_ref_GRCh37_p7 \
    -dbs_retain virus -out_dir deconseq/ \
    -id $FILE -i 90 -c 95 -group 1
 ```
 
# BBmerge - meerge paired end reads
```bash
~/bbmap/bbmerge.sh 
in1=$FILEF \ 
in2=$FILER out=${FILEO%}_merged.fa \
outu=${FILEO%}_unmereged.fa hist=${FILEO%}_histogram.txt
```
# xtract_forward

xtract_forward allows you to extract your unmmerged high quality R1 reads.

After merging your paired end reads programms typically give you 1 R1 and R2 merged file and 1 file that contained all your R1 an R2 reads that did not merge. If doing a simple classificaiton (e.g., Blast) you could end up throwing away useful R1 data from your analysis. xtract-forward allows you to extract all your R1 reads from your unmerged file so you can then concatinate it with your R1/R2 merged file.


# get_tax_lineage

Script allows you to change the headers of fna files downloaded and concatinated from NCBI ref seq. After this script
headers should include nucleotide Accesion number followed by full taxonomic lineage

# blastn script
```bash
ncbi-blast-2.6.0+/bin/blastn \
  -query File1.fasta \
  -outfmt "6 stitle" -out File1.blastn.txt \
  -db viral -dust no \
  -max_target_seqs 1 -perc_identity 90 -evalue 0.0001 -min_raw_gapped_score 105
```
