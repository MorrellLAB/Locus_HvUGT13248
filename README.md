# Locus_HvUGT13248

Locus of interest for Gerit Bethke's project. Panel includes wild, landrace, and elite samples that were previously published in other studies.

Associated transcript name: HORVU.MOREX.r2.5HG0384710.1
- Transcript found in: `/panfs/jay/groups/9/morrellp/shared/References/Reference_Sequences/Barley/Morex_v2/gene_annotation/Barley_Morex_V2_gene_annotation_PGSB.HC.parts.sorted.gff3`

Location relative to Morex v2 (2019): `chr5H:330626689-330629279`

Location relative to parts (Morex v2 chromosomes split into two parts at the centromere): `chr5H_part2:124636877-124639467`

## Methods

Samples were processed with a larger set of samples. Here, we pull the region of interest and subset the previously published samples from the larger set of samples. Detailed sequence processing parameters, filtering criteria, and scripts used for the sequence data processing are available through the GitHub repo: https://github.com/MorrellLAB/Selective_Sweeps/tree/master/00_sequence_processing (this will be made public upon publication of that study).

#### Prepare subset of previously published samples.

Check number of variants in region of interest (Note: VCF has positions relative to chromosome parts):

```bash
# Dependencies
module load bcftools/1.10.2
module load gatk/4.1.2

# In dir: ~/Alignments/selective_sweeps_morex_v2/all/Variant_Recalibrator
# Pull subset of samples
bcftools view --regions chr5H_part2:124636877-124639467 --samples-file ~/GitHub/Locus_HvUGT13248/ssw_subset_published_sample_names.txt ssw_vc_snps_final.vcf.gz > ~/scratch/temp_published_samples_ssw_vc_snps_final_chr5H_part2_124636877-124639467.vcf
# Check number of samples
grep "#CHROM" ~/scratch/temp_published_samples_ssw_vc_snps_final_chr5H_part2_124636877-124639467.vcf | cut -f 10- | tr '\t' '\n' | wc -l
458
# Remove sites that aren't polymorphic (minor allele count of 0) and unused alternate alleles.
gatk SelectVariants \
    -V "/scratch.global/liux1299/temp_published_samples_ssw_vc_snps_final_chr5H_part2_124636877-124639467.vcf" \
    --exclude-non-variants true \
    --remove-unused-alternates true \
    -O "/scratch.global/liux1299/ssw_vc_snps_final_published_samp_chr5H_part2_124636877-124639467_polymorphic.vcf.gz" \
    --tmp-dir "/scratch.global/liux1299/gatk_temp"

# In dir: ~/scratch
# Checks
zgrep -v "#" ssw_vc_snps_final_published_samp_chr5H_part2_124636877-124639467_polymorphic.vcf.gz | wc -l
33

bcftools filter -i 'GT="alt"' /scratch.global/liux1299/ssw_vc_snps_final_published_samp_chr5H_part2_124636877-124639467_polymorphic.vcf.gz | grep -v "#" | wc -l
33
```

Convert parts positions VCF back to pseudomolecular positions:

```bash
# Dependencies
module load python3/3.8.3_anaconda2020.07_mamba
module load htslib/1.9

~/GitHub/File_Conversions/Barley_Parts_to_Pseudomolecules.py --vcf /scratch.global/liux1299/ssw_vc_snps_final_published_samp_chr5H_part2_124636877-124639467_polymorphic.vcf.gz morex_v2 > ~/scratch/temp_ssw_vc_snps_final_published_samp_chr5H_330626689-330629279_polymorphic.vcf
# Update contig lengths
bcftools reheader --fai ~/Shared/References/Reference_Sequences/Barley/Morex_v2/Barley_Morex_V2_pseudomolecules.fasta.fai ~/scratch/temp_ssw_vc_snps_final_published_samp_chr5H_330626689-330629279_polymorphic.vcf > ~/scratch/ssw_vc_snps_final_published_samp_chr5H_330626689-330629279_polymorphic.vcf
# Bgzip and tabix index
bgzip ~/scratch/ssw_vc_snps_final_published_samp_chr5H_330626689-330629279_polymorphic.vcf
tabix -p vcf /scratch.global/liux1299/ssw_vc_snps_final_published_samp_chr5H_330626689-330629279_polymorphic.vcf.gz
```

Move files to store in this Github repo for now since they are pretty small.

```bash
mv /scratch.global/liux1299/ssw_vc_snps_final_published_samp_chr5H_330626689-330629279_polymorphic.vcf* ~/GitHub/Locus_HvUGT13248/data
```
