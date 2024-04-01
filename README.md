# gwas-tools
A set of miscellaneous tools for GWAS data processing and annotation.

## Locus annotation of GWAS associations
The ```annotate_with_loci.R``` script can be used to annotate a set of GWAS summary statistics with the corresponding genomic locus/chromosomal arm of each SNP. These annotations can be particularly useful for identifying independent associations and for Manhattan plot labels.

The repository sub-folder ```chr_locus_refs``` contains reference files of locus coordinates for hg19 and hg38 that were downloaded from the UCSC Genome Browser.

The following is an example of how the script can be run using a set of parallel batch submissions to annotate an entire set of GWAS summary statistics based on hg19 coordinates by chromosome:
```
for i in {1..22}; do
sbatch \
--job-name=chr$i \
--account=account \
--nodes=1 \
--ntasks=1 \
--cpus-per-task=1 \
--mem=64G \
--time=1-00:00:00 \
--wrap="Rscript annotate_with_loci.R \
-i metal_meta_analysis_afr_eur_eas.reformat_se_method.txt \
-l chr_locus_refs/cytoBand.hg19.txt.gz \
-c $i \
-cc 8 \
-pc 9 \
-d /home \
-n metal_meta_analysis_afr_eur_eas.reformat_se_method.arm.txt"
done
```

The output will be a separate file for each chromosome, with locus annotations appended as a new column. These individual files can be concatenated into a single set of annotated summary statistics using a shell command such as the following:
```
head -n 1 chr1_metal_meta_analysis_afr_eur_eas.reformat_se_method.arm.txt > metal_meta_analysis_afr_eur_eas.reformat_se_method.arm.txt

for chr in {1..22}; do \
echo $chr
tail -n +2 -q chr$chr\_metal_meta_analysis_afr_eur_eas.reformat_se_method.arm.txt >> metal_meta_analysis_afr_eur_eas.reformat_se_method.arm.txt
done
```
