# Genomics Tutorial 2023_2024 🌟

For this tutorial, students will be shown how to:
1. **Manipulate VCFs** using both BCFtools and VCFtools. 🛠️
2. **Annotate a VCF** using ANNOVAR and identify variants of interest. 🧬

### Data 📂
Students are provided with VCFs from the TruSeq Cardio Panel sequenced using Illumina:
- **Demonstration File:** `NA12877-r19_S41.vcf`
- **Practice File:** `SAMPLE2.vcf` (copy located in each student directory at `/home/data/tapm/genomics/2023_2024/STUDENT_NAME`)

---

## Set-Up Instructions 💻

1. **Log in to the RCSI Compute System**:
   - Navigate to: `connect.rcsi.com` or `rcsiglobal.cloud.com/Citrix/StoreWeb/#/home` 🌐
   - Sign in and select the correct machine.

2. **Open PuTTY**:
   - Host Name: `prdubrhpc1.research.rcsi.com`
   - Adjust settings:
     - `Connection -> SSH -> Auth -> GSSAPI`: Uncheck **'Attempt GSSAPI authentication (SSH-2 only)'** and **'Attempt GSSAPI key exchange (SSH-2 only)'**. 🚀
     - `Connection -> SSH -> Auth`: Uncheck **'Attempt authentication using Pageant'** and check **'Attempt 'keyboard-interactive' auth (SSH-2)'**. 🔑

3. **File Navigation**:
   - Use File Explorer to access directories: `\\data.research.rcsi.com\tapm\genomics\2023_2024\STUDENT_NAME`. 📁

---

## 1. BCFtools 🧰
**BCFtools** is widely used as part of NGS pipelines for variant calling and manipulating VCFs/BCFs. [More Info](http://samtools.github.io/bcftools/bcftools.html)

**Load Module:**
```bash
module purge
module load BCFtools/1.12-GCC-10.2.0
```

### Tasks:

#### Task 1.1: Zip a VCF 📦
Compress a large VCF file:
```bash
bgzip /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf
```
To uncompress:
```bash
gunzip /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
```

#### Task 1.2: Index a VCF 🗂️
Create an index file for fast data retrieval:
```bash
tabix /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
```

#### Task 1.3: View VCF or Header 📋
- Full VCF:
  ```bash
  bcftools view /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```
- Header only:
  ```bash
  bcftools view -h /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```

#### Task 1.4: View Specific Locations 🌍
- Specific chromosome:
  ```bash
  bcftools view -r chr11 /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```
- Specific region:
  ```bash
  bcftools view -r chr11:116820959-116821618 /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz
  ```

---

## 2. VCFtools 🧪
**VCFtools** is designed for filtering, comparing, converting, merging, and subsetting VCF files. [More Info](http://vcftools.sourceforge.net/man_latest.html)

**Load Module:**
```bash
module purge
module load VCFtools/0.1.16-GCC-9.3.0/
```

### Tasks:

#### Task 2.1: Include or Exclude Chromosomes 🧭
- Include only chromosome 11:
  ```bash
  vcftools --chr chr11 --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out chr11_variants
  ```
- Exclude chromosome 11:
  ```bash
  vcftools --not-chr chr11 --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out exclude_chr11
  ```

#### Task 2.2: Use BED Files 🛏️
- Include variants from `regions.bed`:
  ```bash
  vcftools --bed /home/data/tapm/genomics/ref/regions.bed --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out filtered_variants
  ```
- Exclude variants from `regions.bed`:
  ```bash
  vcftools --exclude-bed /home/data/tapm/genomics/ref/regions.bed --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out exclude_bed
  ```

#### Task 2.3: Filter by Quality 🎯
Filter for genotype quality (GQ > 90):
```bash
vcftools --minGQ 90 --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out high_quality
```

#### Task 2.4: Filter by Depth 🕳️
Filter for sequencing depth (DP ≥ 500):
```bash
vcftools --minDP 500 --gzvcf /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz --recode --out high_depth
```

---

## 3. ANNOVAR 🖋️
**ANNOVAR** annotates variants with gene, frequency, and pathogenicity information. [More Info](https://annovar.openbioinformatics.org/en/latest/)

**Load Module:**
```bash
module purge
module load annovar/20191024-GCCcore-8.2.0-Perl-5.28.1
```

### Task 3.1: Annotate VCF ✍️
Annotate with gene, minor allele frequency (gnomAD), and pathogenicity (ClinVar):
```bash
table_annovar.pl /home/data/tapm/genomics/2023_2024/demo/NA12877-r19_S41.vcf.gz \
  /home/data/tapm/genomics/ref/humandb -buildver hg38 \
  -out annotated_file --remove \
  --protocol refGene,gnomad_exome,clinvar_20210501 \
  --operation g,f,f -nastring . --vcfinput --polish
```

---

## Student Task 🎓
Repeat the above commands using `SAMPLE2.vcf`.

### Questions:
1. Does the patient have a known pathogenic variant in any gene? 🧬
2. What is the variant position, cDNA, and amino acid change? 📍
3. What type of variant is this? 🧫
4. What is the frequency of the variant in the gnomAD control population? 🌐

---

**Happy Learning! 😊**
