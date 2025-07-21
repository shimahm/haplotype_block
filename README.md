# Haplotype Block Detection Pipeline (LD-based method)

This code is a three-step pipeline for haplotype block detection using VCF and PLINK tools. Here’s a breakdown of each command in scientific but simplified bioinformatics terms:

---

## 🔹 Step 1: Convert VCF to PLINK format

```bash
vcftools --gzvcf gwas_ready_filtered.vcf.gz --remove-indels --plink --chrom-map chr.txt --out F2
```

**What it does:**

- **Input:** A VCF file (`gwas_ready_filtered.vcf.gz`) containing variant calls for multiple individuals.
- **Goal:** Converts the VCF file to PLINK’s PED/MAP format after:
  - Removing indels (keeping only SNPs)
  - Mapping chromosome names using `chr.txt` (e.g., mapping A01 → 1)
- **Output:** `F2.ped` and `F2.map` (PLINK-format genotype data).

---

## 🔹 Step 2: Create PLINK binary files (BED format)

```bash
plink --ped F2.ped --map F2.map --geno 0.90 --allow-extra-chr --chr-set 27 --make-bed --out F2
```

**What it does:**

- Reads the `F2.ped` and `F2.map` files.
- Filters out SNPs with >90% missing data (`--geno 0.90`).
- Allows for non-standard chromosome names (`--allow-extra-chr`) and sets the max number of chromosomes to 27 (`--chr-set 27`) — suitable for Brassica species.
- Converts the data into binary PLINK format (`.bed`, `.bim`, `.fam`), which is more efficient.
- **Output:** `F2.bed`, `F2.bim`, and `F2.fam`.

---

## 🔹 Step 3: Detect haplotype blocks

```bash
plink --bfile F2 --blocks no-pheno-req --blocks-max-kb 200 --blocks-min-maf 0.05 --blocks-strong-lowci 0.7 --blocks-strong-highci 0.98 --out myblocks
```

**What it does:**

- Performs haplotype block detection (based on linkage disequilibrium between SNPs).
- **Options explained:**
  - `--blocks no-pheno-req`: Don’t require phenotype data.
  - `--blocks-max-kb 200`: Max block size is 200 kb.
  - `--blocks-min-maf 0.05`: Use only SNPs with MAF ≥ 5%.
  - `--blocks-strong-lowci 0.7` and `--blocks-strong-highci 0.98`: Define confidence intervals for strong LD between SNPs (D' measure).
- **Output:** A file (`myblocks.blocks.det`) listing identified haplotype blocks.

---

## 🔬 In summary

This pipeline:

- Converts a VCF file to PLINK format, keeping only SNPs.
- Filters low-quality SNPs and converts to a binary format.
- Detects haplotype blocks based on LD patterns, suitable for downstream analysis like association studies or population structure.

---

## Full Pipeline Code

```bash
# haplotype block Fei Version:

# Step 1: Convert VCF to PLINK format
vcftools --gzvcf gwas_ready_filtered.vcf.gz --remove-indels --plink --chrom-map chr.txt --out F2

# Step 2: Create PLINK binary files (BED format)
plink --ped F2.ped --map F2.map --geno 0.90 --allow-extra-chr --chr-set 27 --make-bed --out F2

# Step 3: Detect haplotype blocks
plink --bfile F2 --blocks no-pheno-req --blocks-max-kb 200 --blocks-min-maf 0.05 --blocks-strong-lowci 0.7 --blocks-strong-highci 0.98 --out myblocks
```
