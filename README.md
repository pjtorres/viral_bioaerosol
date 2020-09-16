#Must have the following for analysis

- python 3 or greater
- [Biopython](https://biopython.org/)
- [trimmomatic](http://www.usadellab.org/cms/?page=trimmomatic)
- [prinseq](http://prinseq.sourceforge.net/)
- [deconseq](http://deconseq.sourceforge.net/)
- [bbmerge](https://jgi.doe.gov/data-and-tools/bbtools/bb-tools-user-guide/bbmerge-guide/)
- [blastn](https://www.ncbi.nlm.nih.gov/books/NBK279680/)

# Trimmomatic command-  remove sequence adaptors
```java
java -jar ~/Trimmomatic-0.36/trimmomatic-0.36.jar PE $FILEF $FILER paired_output_${FILEF%} \
unpaired_output_${FILEF%} paired_output_${FILER%} unpaired_output_${FILER%} -trimlog output.log \
ILLUMINACLIP:TruSeq3-PE-2.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:60
```
# Prinseq script - used for quality control and data processing
```perl
perl ~/prinseq-lite-0.20.4/prinseq-lite.pl -verbose -derep 1245 -lc_method entropy \
-lc_threshold 50 -trim_qual_right 20 -trim_qual_left 20 -trim_qual_type mean \
-trim_qual_rule lt -trim_qual_window 2 -trim_qual_step 1 -trim_tail_left 5 \
-trim_tail_right 5 -min_len 60 -min_qual_mean 25 -ns_max_p 1 \
-fastq $FILEA -fastq2 $FILEB -log ${FILEA%}_prinseq.log -out_bad ${FILEA%}_prinseq_bad \
-out_good ${FILEA%}_prinseq_good
```
# Deconseq script for removal of potential Human Contamination -  does not handle PE reads so each file ran seperately
 ```perl 
 perl deconseq-standalone-0.4.3/bwasw_modified_source/deconseq.pl \
    -f file.fq \
    -dbs hs_ref_GRCh37_p7 \
    -dbs_retain virus -out_dir deconseq/ \
    -id $FILE -i 90 -c 95 -group 1
 ```
# fastq-pair Match up paired end fastq files quickly and efficiently - [Edwards lab](https://github.com/linsalrob/fastq-pair)
```bash
fastq_pair $FILEF.fastq $FILER.fastq
```

# BBmerge - merge paired end reads
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
Will need a file that only contains the accesion numbers in your fna file. Below is an example of how you can get that from your fna file:

`bash
sed 's/\s.*$//' subset_viral_genomic.fna | grep ">" | sed 's/>//' | awk '{print $1}' > subset_viral_accesion.txt
`

Script allows you to change the headers of fna files downloaded and concatinated from NCBI ref seq. After this script
headers should include nucleotide Accesion number followed by full taxonomic lineage

 `python get_taxlineage_fna.py --acc test/subset_viral_accesion.txt --input test/subset_viral_genomic.fna --output  test/viral_subset.genomic.new.header.fna`

# blastn script
```bash
ncbi-blast-2.6.0+/bin/blastn \
  -query File1.fasta \
  -outfmt "6 stitle" -out File1.blastn.txt \
  -db viral -dust no \
  -max_target_seqs 1 -perc_identity 90 -evalue 0.0001 -min_raw_gapped_score 105
```
