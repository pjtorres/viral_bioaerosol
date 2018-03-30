# xtract_forward

xtract_forward allows you to extract your unmmerged high quality R1 reads.

After merging your paired end reads programms typically give you 1 R1 and R2 merged file and 1 file that contained all your R1 an R2 reads that did not merge. If doing a simple classificaiton (e.g., Blast) you could end up throwing away useful R1 data from your analysis. xtract-forward allows you to extrct all your R1 reads from your unmerged file so you can then concatinate it with your R1/R2 merged file.


# get_tax_lineage

Script allows you to change the headers of fna files downloaded and concatinated from NCBI ref seq. After this script
headers should include nucleotide Accesion number followed by full taxonomic lineage
