# Genomics Tutorial 2023_2024 🌟

For this tutorial, students will be shown how to:
1. **Manipulate VCFs** using both BCFtools and VCFtools. 🛠️
2. **Annotate a VCF** using ANNOVAR and identify variants of interest. 🧬

### Data 📂
Students are provided with VCFs from the TruSeq Cardio Panel sequenced using Illumina:
- **Demonstration File:** `NA12877-r19_S41.vcf`
- **Practice File:** `SAMPLE2.vcf`
- Both located in  `/home/data/tapm/cb/genomics1/2025_2026/vcfs`)

### Tools Overview 🧰

The tools introduced in this tutorial play a crucial role in genomic data analysis, particularly in handling and interpreting Variant Call Format (VCF) files, which are commonly used in next-generation sequencing (NGS) workflows.

1. **BCFtools**: A powerful tool for manipulating VCF and BCF files, enabling efficient operations such as compression, indexing, and querying. It's crucial for working with large VCF files, as it supports the fast retrieval of data and manipulation of variant call data. [More Info](http://samtools.github.io/bcftools/bcftools.html)

2. **VCFtools**: Designed specifically for filtering, comparing, and analyzing VCF files, this tool helps in narrowing down variants of interest based on criteria like chromosome, genotype quality (GQ), and sequencing depth (DP). It simplifies the process of preparing data for downstream analysis by efficiently parsing large VCF files. [More Info](http://vcftools.sourceforge.net/man_latest.html)

3. **ANNOVAR**: This tool annotates genetic variants with additional information such as gene names, minor allele frequency (gnomAD), and pathogenicity (ClinVar). It's invaluable in clinical genomics for identifying whether a variant is known to be pathogenic or benign. [More Info](https://annovar.openbioinformatics.org/en/latest/)

---

## Set-Up Instructions 💻

 **Open PuTTY**:
   - Host Name: `prdubrhpc1.research.rcsi.com`
   - Adjust settings:
     - `Connection -> SSH -> Auth -> GSSAPI`: Uncheck **'Attempt GSSAPI authentication (SSH-2 only)'** and **'Attempt GSSAPI key exchange (SSH-2 only)'**. 🚀
     - `Connection -> SSH -> Auth`: Uncheck **'Attempt authentication using Pageant'** and check **'Attempt 'keyboard-interactive' auth (SSH-2)'**. 🔑

---

## 1. Getting Started
**Create a folder** in `/home/data/tapm/cb/genomics1/2025_2026/`)
```bash
cd /home/data/tapm/cb/genomics1/2025_2026/
mkdir your_full_name
```
- make sure to use lowercase and _ instead of a space!

**Copy VCF files** in `/home/data/tapm/cb/genomics1/2025_2026/vcfs`)
```bash
cp /home/data/tapm/cb/genomics1/2025_2026/vcfs/*vcf /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]
```
- make sure to replace your full name with the name of your folder!

**move to your folder**
```bash
cd /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]

pwd

ls
```
- pwd should print out your current directory and you should see the above
- ls will list all the files in your folder, you should see the 2 VCF files there now
- **if you don't see the VCF files** ask the lecturer or demonstrators for help!

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
bgzip /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf
```

#### Task 1.2: Index a VCF 🗂️
Create an index file for fast data retrieval:
```bash
tabix /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz
```

#### Task 1.3: View VCF or Header 📋
- Full VCF:
  ```bash
  bcftools view /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz
  ```
- Header only:
  ```bash
  bcftools view -h /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz
  ```

#### Task 1.4: View Specific Locations 🌍
- Specific chromosome:
  ```bash
  bcftools view -r chr11 /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz
  ```
- Specific region:
  ```bash
  bcftools view -r chr11:116820959-116821618  /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz
  ```

---

## 2. VCFtools 🧪
**VCFtools** is designed for filtering, comparing, converting, merging, and subsetting VCF files. [More Info](http://vcftools.sourceforge.net/man_latest.html)

- As for BCFtools, VCFtools can also be used to filter a VCF based on position.
- --gvcf is used to load a gzipped VCF file 
- --out is used to specify an output file

**Load Module:**
```bash
module purge
module load VCFtools/0.1.16-GCC-9.3.0/
```

### Tasks:

#### Task 2.1: Include or Exclude Chromosomes 🧭
- Include only chromosome 11:
  ```bash
  vcftools --chr chr11 --gzvcf /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz --recode --out /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41_chr11
  ```
- Exclude chromosome 11:
  ```bash
  vcftools --not-chr chr11 --gzvcf  /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz --recode --out  /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41_excluding_chr11
  ```

#### Task 2.2: Use BED Files 🛏️
- A bed file is a tab delimited text file which is used to store genomic regions as coordinates. It contains at a minimum three columns (chrom, chromStart and chromEnd).
- A bed file of regions of interest can be used by VCFtools for filtering of variants. A site will be kept or excluded if any part of any allele (REF or ALT) at a site is within the range of one of the BED entries.
- You have been provided with a sample bed file called regions.bed at /home/data/tapm/genomics/ref which contains a list of sites on chromosome 2.

- Include variants from `regions.bed`:
  ```bash
  vcftools --gzvcf /home/data/tapm/cb/genomics1/2025_2026/laura_whelan/NA12877-r19_S41.vcf.gz \
  --bed /home/data/tapm/cb/genomics1/2025_2026/ref/regions.bed \
  --recode \
  --out filtered_variants
  ```
- Exclude variants from `regions.bed`:
  ```bash
  vcftools --exclude-bed /home/data/tapm/cb1/genomics1/2024_2025/ref/regions.bed --gzvcf /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz --recode --out exclude_bed
  ```

#### Task 2.3: Filter by Quality 🎯
Filter for genotype quality (GQ > 90):
```bash
vcftools --minGQ 90 --gzvcf /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz --recode --out high_quality
```

#### Task 2.4: Filter by Depth 🕳️
Filter for sequencing depth (DP ≥ 500):
```bash
vcftools --minDP 500 --gzvcf /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz --recode --out high_depth
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
table_annovar.pl /home/data/tapm/cb/genomics1/2025_2026/[your_full_name]/NA12877-r19_S41.vcf.gz \
  /home/data/tapm/cb/genomics1/2025_2026/ref/humandb -buildver hg38 \
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
