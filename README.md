# Genomics 1 Tutorial Resources (2024-2025)

Teaching and student resources for the Genomics 1 Tutorial as part of the Computational Biology 1 module in the MSc in Technologies and Analytics in Precision Medicine.

---

## Overview
This tutorial introduces students to the essential tools and techniques for working with genomic data:

1. **Manipulating VCF files** using BCFtools and VCFtools.
2. **Annotating VCF files** with ANNOVAR to identify variants of interest.

### Data
- **Demonstration File:** `NA12877-r19_S41.vcf`
- **Practice File:** `SAMPLE2.vcf` (Located in each student's directory at `/home/data/tapm/genomics/2023_2024/STUDENT_NAME`)

---

## Set-Up Instructions

1. **Log in to the RCSI Compute System**:
   - Navigate to: `connect.rcsi.com` or `rcsiglobal.cloud.com/Citrix/StoreWeb/#/home`
   - Sign in and select the correct machine.

2. **Open PuTTY**:
   - Host Name: `prdubrhpc1.research.rcsi.com`
   - Adjust settings:
     - `Connection -> SSH -> Auth -> GSSAPI`: Uncheck **'Attempt GSSAPI authentication (SSH-2 only)'** and **'Attempt GSSAPI key exchange (SSH-2 only)'**.
     - `Connection -> SSH -> Auth`: Uncheck **'Attempt authentication using Pageant'** and check **'Attempt 'keyboard-interactive' auth (SSH-2)'**.

3. **File Navigation**:
   - Use File Explorer to access directories: `\\data.research.rcsi.com\tapm\genomics\2023_2024\STUDENT_NAME`.

---

## Tools and Tasks

### 1. BCFtools
**BCFtools** is a powerful tool for manipulating VCFs and BCFs. [More Info](http://samtools.github.io/bcftools/bcftools.html)

**Load Module:**
```bash
module purge
module load BCFtools/1.12-GCC-10.2.0
```

#### Tasks:
- **Zip a VCF:**
  ```bash
  bgzip /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf
  ```
- **Index a VCF:**
  ```bash
  tabix /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```
- **View VCF Header:**
  ```bash
  bcftools view -h /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```
- **View Specific Regions:**
  ```bash
  bcftools view -r chr11:116820959-116821618 /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```

---

### 2. VCFtools
**VCFtools** specializes in filtering, comparing, and converting VCF files. [More Info](http://vcftools.sourceforge.net/man_latest.html)

**Load Module:**
```bash
module purge
module load VCFtools/0.1.16-GCC-9.3.0/
```

#### Tasks:
- **Filter by Chromosome:**
  ```bash
  vcftools --chr chr11 --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out chr11_variants
  ```
- **Filter by BED File:**
  ```bash
  vcftools --bed regions.bed --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out filtered_variants
  ```
- **Filter by Quality (GQ > 90):**
  ```bash
  vcftools --minGQ 90 --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out high_quality
  ```

---

### 3. ANNOVAR
**ANNOVAR** annotates variants with gene, frequency, and pathogenicity information. [More Info](https://annovar.openbioinformatics.org/en/latest/)

**Load Module:**
```bash
module purge
module load annovar/20191024-GCCcore-8.2.0-Perl-5.28.1
```

#### Annotate VCF:
```bash
table_annovar.pl /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz \
  /home/data/tapm/genomics/ref/humandb -buildver hg38 \
  -out annotated_file --remove \
  --protocol refGene,gnomad_exome,clinvar_20210501 \
  --operation g,f,f -nastring . --vcfinput --polish
```

---

## Student Task
- Use `SAMPLE2.vcf` to replicate all the commands demonstrated in this tutorial.
- Answer the following questions:
  1. Does the patient have a known pathogenic variant in any gene?
  2. What is the variant position, cDNA, and amino acid change?
  3. What type of variant is this?
  4. What is the frequency of the variant in the gnomAD control population?

---

**Happy Learning!**
