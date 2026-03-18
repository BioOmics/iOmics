# iOmics
Simple, rapid analysis of omics data

# Bulk ATAC-Seq Analysis Pipeline

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Bash](https://img.shields.io/badge/language-bash-blue.svg)](https://www.gnu.org/software/bash/)
[![Bioinformatics](https://img.shields.io/badge/bioinformatics-ATAC--seq-green.svg)](https://en.wikipedia.org/wiki/ATAC-seq)

A comprehensive Bash pipeline for processing bulk ATAC-Seq data, from raw data to peak calling and visualization files.

## 📝 Description

This pipeline automates the processing of bulk ATAC-Seq data, handling various input formats and performing quality control, alignment, peak calling, and bigwig file generation. It's designed to be user-friendly while maintaining flexibility for advanced users.

## ✨ Features

- **Multiple input formats**: Supports SRA, FASTQ (single/paired-end), BAM, and SAM files
- **Comprehensive quality control**: Uses fastp for read trimming and quality filtering
- **Efficient alignment**: BWA for read alignment
- **Duplicate removal**: Sambamba for marking and removing PCR duplicates
- **Peak calling**: MACS3 for identifying accessible chromatin regions
- **Visualization**: BigWig file generation with deepTools
- **Resume capability**: Skip completed steps with `-s` option
- **Clean output**: Optional removal of intermediate files

## 🔧 Dependencies

The pipeline requires the following software:

| Software | Purpose | Installation |
|----------|---------|--------------|
| sra-tools | SRA file conversion | `conda install -c bioconda sra-tools` |
| fastp | Quality control | `conda install -c bioconda fastp` |
| bwa | Read alignment | `conda install -c bioconda bwa` |
| samtools | SAM/BAM processing | `conda install -c bioconda samtools` |
| sambamba | Duplicate marking | `conda install -c bioconda sambamba` |
| macs3 | Peak calling | `conda install -c bioconda macs3` |
| deeptools | BigWig generation | `conda install -c bioconda deeptools` |

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/BioOmics/atacseq-pipeline.git
cd atacseq-pipeline

# Make the script executable
chmod +x atacseq-pipeline.sh

# Run the pipeline (single-end FASTQ example)
./atacseq-pipeline.sh -i sample.fq.gz -g genome.fa -a annotation.gtf -t 8
```

## 📋 Usage

```bash
./atacseq-pipeline.sh -i INPUT -g GENOME -a ANNOTATION [OPTIONS]
```

### Required Parameters

| Option | Description |
|--------|-------------|
| `-i, --input` | Input file (SRA, FASTQ, BAM, or SAM) |
| `-I, --input2` | Second input file for paired-end FASTQ |
| `-g, --genome` | Reference genome FASTA file |
| `-a, --annotation` | Annotation file (GTF or GFF3 format) |

### Optional Parameters

| Option | Description | Default |
|--------|-------------|---------|
| `-q, --qualityBase` | Minimum base quality score | 20 |
| `-b, --binSize` | Generate bigwig with specified bin size | No bigwig |
| `-o, --output` | Output directory | `./[input]_pipeline_result` |
| `-t, --threads` | Number of CPU threads | 8 |
| `-f, --force` | Force overwrite output directory | No |
| `-s, --skip` | Skip completed steps | No |
| `-r, --remove` | Remove intermediate files | No |
| `-h, --help` | Show help message | - |
| `-v, --version` | Show version | - |

## 📦 Output Structure

```
[input]_pipeline_result/
├── fastq/           # Clean FASTQ files
├── bam/             # Alignment files (sorted, deduplicated)
├── peaks/           # MACS3 peak calling results
├── bigwig/          # BigWig files for visualization (if -b used)
└── report/          # Statistics and log files
```

## 💡 Examples

### 1. Process SRA file
```bash
./atacseq-pipeline.sh -i SRR12345678.sra -g hg38.fa -a hg38.gtf
```

### 2. Paired-end FASTQ files
```bash
./atacseq-pipeline.sh -i sample_R1.fq.gz -I sample_R2.fq.gz -g mm10.fa -a mm10.gtf -t 16
```

### 3. Generate bigwig file with custom output directory
```bash
./atacseq-pipeline.sh -i sample.bam -g genome.fa -a genes.gtf -b 10 -o ./atac_results -t 8
```

### 4. Resume interrupted run with cleanup
```bash
./atacseq-pipeline.sh -i sample.fq.gz -g genome.fa -a genes.gtf -s -r
```

## 📊 Pipeline Steps

1. **Input conversion** (if SRA): `fasterq-dump` → FASTQ
2. **Quality control**: `fastp` → Clean FASTQ
3. **Alignment**: `bwa mem` → SAM
4. **SAM to BAM**: `samtools view` → BAM
5. **Sorting**: `sambamba sort` → Sorted BAM
6. **Duplicate marking**: `sambamba markdup` → Deduplicated BAM
7. **Indexing**: `samtools index` → BAM index
8. **Peak calling**: `macs3` → Narrow peaks
9. **BigWig generation** (optional): `bamCoverage` → BigWig

## ⚙️ Configuration

The pipeline automatically checks for required software dependencies before execution. Modify the quality threshold or thread count according to your needs using the command-line options.

## 📝 Notes

- For paired-end data, both input files must be provided
- The genome FASTA file must be indexed with `bwa index` beforehand
- Annotation file format (GTF/GFF3) is required for MACS3
- Intermediate files can consume significant disk space; use `-r` to clean up

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/BioOmics/atacseq-pipeline/issues).

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**BioOmics** (haoyuchao@zju.edu.cn)

## 📚 Citation

If you use this pipeline in your research, please cite:

```
@software{atacseq_pipeline,
  author = {BioOmics},
  title = {Bulk ATAC-Seq Analysis Pipeline},
  year = {2024},
  url = {https://github.com/BioOmics/atacseq-pipeline}
}
```

## 🙏 Acknowledgments

- The developers of all dependency tools
- Zhejiang University for support

---


# Bulk ChIP-Seq Analysis Pipeline

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Bash](https://img.shields.io/badge/language-bash-blue.svg)](https://www.gnu.org/software/bash/)
[![Bioinformatics](https://img.shields.io/badge/bioinformatics-ChIP--seq-green.svg)](https://en.wikipedia.org/wiki/ChIP-sequencing)

A comprehensive Bash pipeline for processing bulk ChIP-Seq data, with integrated control sample processing for robust peak calling.

## 📝 Description

This pipeline automates the processing of bulk ChIP-Seq data, handling both IP and control samples simultaneously. It performs quality control, alignment, peak calling with control comparison, and visualization file generation. Designed for reproducibility and ease of use.

## ✨ Features

- **Dual-sample processing**: Simultaneously handles IP and control samples
- **Multiple input formats**: Supports SRA, FASTQ (single/paired-end), BAM, and SAM files
- **Integrated control comparison**: Proper peak calling with input/IgG control
- **Comprehensive quality control**: Fastp for read trimming and quality filtering
- **Efficient alignment**: BWA for read alignment
- **Duplicate removal**: Sambamba for marking and removing PCR duplicates
- **Peak calling**: MACS3 with experimental vs control comparison
- **Visualization**: BigWig file generation with deepTools
- **Resume capability**: Skip completed steps with `-s` option
- **Clean output**: Optional removal of intermediate files

## 🔧 Dependencies

| Software | Purpose | Installation |
|----------|---------|--------------|
| sra-tools | SRA file conversion | `conda install -c bioconda sra-tools` |
| fastp | Quality control | `conda install -c bioconda fastp` |
| bwa | Read alignment | `conda install -c bioconda bwa` |
| samtools | SAM/BAM processing | `conda install -c bioconda samtools` |
| sambamba | Duplicate marking | `conda install -c bioconda sambamba` |
| macs3 | Peak calling | `conda install -c bioconda macs3` |
| deeptools | BigWig generation | `conda install -c bioconda deeptools` |

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/BioOmics/chipseq-pipeline.git
cd chipseq-pipeline

# Make the script executable
chmod +x chipseq-pipeline.sh

# Run the pipeline (single-end FASTQ example)
./chipseq-pipeline.sh -i IP_sample.fq.gz -c control.fq.gz -g genome.fa -a annotation.gtf -t 8
```

## 📋 Usage

```bash
./chipseq-pipeline.sh -i INPUT -c CONTROL -g GENOME -a ANNOTATION [OPTIONS]
```

### Required Parameters

| Option | Description |
|--------|-------------|
| `-i, --input` | IP sample file (SRA, FASTQ, BAM, or SAM) |
| `-I, --input2` | Second IP file for paired-end FASTQ |
| `-c, --control` | Control sample file (SRA, FASTQ, BAM, or SAM) |
| `-C, --control2` | Second control file for paired-end FASTQ |
| `-g, --genome` | Reference genome FASTA file |
| `-a, --annotation` | Annotation file (GTF or GFF3 format) |

### Optional Parameters

| Option | Description | Default |
|--------|-------------|---------|
| `-q, --qualityBase` | Minimum base quality score | 20 |
| `-b, --binSize` | Generate bigwig with specified bin size | No bigwig |
| `-o, --output` | Output directory | `./[input]_pipeline_result` |
| `-t, --threads` | Number of CPU threads | 8 |
| `-f, --force` | Force overwrite output directory | No |
| `-s, --skip` | Skip completed steps | No |
| `-r, --remove` | Remove intermediate files | No |
| `-h, --help` | Show help message | - |
| `-v, --version` | Show version | - |

## 📦 Output Structure

```
[input]_pipeline_result/
├── fastq/
│   ├── IP_clean/       # Clean IP FASTQ files
│   └── control_clean/  # Clean control FASTQ files
├── bam/
│   ├── IP/             # IP alignment files
│   └── control/        # Control alignment files
├── peaks/              # MACS3 peak calling results (IP vs control)
├── bigwig/             # BigWig files for visualization (if -b used)
└── report/             # Statistics and log files
```

## 💡 Examples

### 1. Process SRA files with control
```bash
./chipseq-pipeline.sh -i IP_sample.sra -c control.sra -g hg38.fa -a hg38.gtf
```

### 2. Paired-end FASTQ files
```bash
./chipseq-pipeline.sh -i IP_R1.fq.gz -I IP_R2.fq.gz -c control_R1.fq.gz -C control_R2.fq.gz -g mm10.fa -a mm10.gtf -t 16
```

### 3. Process existing BAM files
```bash
./chipseq-pipeline.sh -i IP.bam -c control.bam -g genome.fa -a genes.gtf -b 10
```

### 4. Resume interrupted run with cleanup
```bash
./chipseq-pipeline.sh -i IP.fq.gz -c control.fq.gz -g genome.fa -a genes.gtf -s -r
```

## 📊 Pipeline Steps

1. **Input conversion** (if SRA): `fasterq-dump` → FASTQ (both samples)
2. **Quality control**: `fastp` → Clean FASTQ (both samples)
3. **Alignment**: `bwa mem` → SAM (both samples)
4. **SAM to BAM**: `samtools view` → BAM (both samples)
5. **Sorting**: `sambamba sort` → Sorted BAM (both samples)
6. **Duplicate marking**: `sambamba markdup` → Deduplicated BAM (both samples)
7. **Indexing**: `samtools index` → BAM index (both samples)
8. **Peak calling**: `macs3` with treatment vs control → Narrow peaks
9. **BigWig generation** (optional): `bamCoverage` → BigWig (both samples)

## ⚙️ Important Notes for ChIP-Seq

- **Control samples are essential**: Always provide appropriate control (input/IgG) for proper peak calling
- **Paired-end consistency**: If using paired-end data for IP, control must also be paired-end
- **Library compatibility**: IP and control samples should be sequenced with the same library preparation method
- **Peak calling parameters**: MACS3 automatically adjusts for control sample size differences

## 📝 Notes

- Both IP and control files must be in the same format (e.g., both SRA or both FASTQ)
- The genome FASTA file must be indexed with `bwa index` beforehand
- For paired-end data, both pairs must be provided for each sample
- Control sample processing follows the same QC and alignment steps as IP

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/BioOmics/chipseq-pipeline/issues).

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**BioOmics** (haoyuchao@zju.edu.cn)

## 📚 Citation

If you use this pipeline in your research, please cite:

```
@software{chipseq_pipeline,
  author = {BioOmics},
  title = {Bulk ChIP-Seq Analysis Pipeline},
  year = {2024},
  url = {https://github.com/BioOmics/chipseq-pipeline}
}
```

## 🙏 Acknowledgments

- The developers of all dependency tools
- Zhejiang University for support

---
# Bulk RNA-Seq Quantification Pipeline

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Bash](https://img.shields.io/badge/language-bash-blue.svg)](https://www.gnu.org/software/bash/)
[![Bioinformatics](https://img.shields.io/badge/bioinformatics-RNA--seq-red.svg)](https://en.wikipedia.org/wiki/RNA-Seq)

A comprehensive Bash pipeline for processing bulk RNA-Seq data, from raw reads to gene expression quantification and visualization.

## 📝 Description

This pipeline automates the processing of bulk RNA-Seq data, handling various input formats and performing quality control, splice-aware alignment, transcript quantification, and bigwig file generation. It uses STAR for fast and accurate alignment and RSEM for precise transcript/gene-level quantification.

## ✨ Features

- **Multiple input formats**: Supports SRA, FASTQ (single/paired-end), BAM, and SAM files
- **Splice-aware alignment**: STAR for accurate mapping across splice junctions
- **Transcript quantification**: RSEM for gene and isoform-level expression estimates
- **Comprehensive quality control**: Fastp for read trimming and quality filtering
- **Duplicate awareness**: Sambamba for marking duplicates (optional)
- **Visualization**: BigWig file generation with deepTools
- **Resume capability**: Skip completed steps with `-s` option
- **Clean output**: Optional removal of intermediate files

## 🔧 Dependencies

| Software | Purpose | Installation |
|----------|---------|--------------|
| sra-tools | SRA file conversion | `conda install -c bioconda sra-tools` |
| fastp | Quality control | `conda install -c bioconda fastp` |
| STAR | Splice-aware alignment | `conda install -c bioconda star` |
| rsem | Gene/transcript quantification | `conda install -c bioconda rsem` |
| sambamba | BAM processing | `conda install -c bioconda sambamba` |
| deeptools | BigWig generation | `conda install -c bioconda deeptools` |

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/Haoyu-Chao/rnaseq-pipeline.git
cd rnaseq-pipeline

# Make the script executable
chmod +x rnaseq-pipeline.sh

# Run the pipeline (single-end FASTQ example)
./rnaseq-pipeline.sh -i sample.fq.gz -g genome.fa -a annotation.gtf -t 8
```

## 📋 Usage

```bash
./rnaseq-pipeline.sh -i INPUT -g GENOME -a ANNOTATION [OPTIONS]
```

### Required Parameters

| Option | Description |
|--------|-------------|
| `-i, --input` | Input file (SRA, FASTQ, BAM, or SAM) |
| `-I, --input2` | Second input file for paired-end FASTQ |
| `-g, --genome` | Reference genome FASTA file |
| `-a, --annotation` | Annotation file (GTF or GFF3 format) |

### Optional Parameters

| Option | Description | Default |
|--------|-------------|---------|
| `-q, --qualityBase` | Minimum base quality score | 20 |
| `-b, --binSize` | Generate bigwig with specified bin size | No bigwig |
| `-o, --output` | Output directory | `./[input]_pipeline_result` |
| `-t, --threads` | Number of CPU threads | 8 |
| `-f, --force` | Force overwrite output directory | No |
| `-s, --skip` | Skip completed steps | No |
| `-r, --remove` | Remove intermediate files | No |
| `-h, --help` | Show help message | - |
| `-v, --version` | Show version | - |

## 📦 Output Structure

```
[input]_pipeline_result/
├── fastp/               # Fastp quality control reports (HTML/JSON)
├── bam/                 # Sorted BAM files with indexes
├── quantification/      
│   ├── rsem/            # RSEM output files (genes/isoforms results)
│   └── counts/          # Raw count matrices
├── bigwig/              # BigWig files for visualization (if -b used)
└── logs/                # Pipeline execution logs
```

### Quantification Output Files

RSEM generates multiple output files:
- `.genes.results`: Gene-level expression estimates (TPM/expected counts)
- `.isoforms.results`: Transcript-level expression estimates
- `.transcript.bam`: Transcriptome-aligned BAM file
- `.stat`: Alignment statistics

## 💡 Examples

### 1. Process SRA file
```bash
./rnaseq-pipeline.sh -i SRR12345678.sra -g hg38.fa -a hg38.gtf
```

### 2. Paired-end FASTQ files
```bash
./rnaseq-pipeline.sh -i sample_R1.fq.gz -I sample_R2.fq.gz -g mm10.fa -a mm10.gtf -t 16
```

### 3. Generate bigwig file for visualization
```bash
./rnaseq-pipeline.sh -i sample.bam -g genome.fa -a genes.gtf -b 10 -o ./rnaseq_results
```

### 4. Resume interrupted run with cleanup
```bash
./rnaseq-pipeline.sh -i sample.fq.gz -g genome.fa -a genes.gtf -s -r
```

## 📊 Pipeline Steps

1. **Input conversion** (if SRA): `fasterq-dump` → FASTQ
2. **Quality control**: `fastp` → Clean FASTQ + HTML report
3. **Genome index preparation** (if needed): `STAR --runMode genomeGenerate`
4. **Alignment**: `STAR --runMode alignReads` → SAM
5. **BAM processing**: `samtools view → sambamba sort → sambamba markdup`
6. **RSEM preparation**: `rsem-prepare-reference` (if needed)
7. **Quantification**: `rsem-calculate-expression` → Gene/transcript counts
8. **BigWig generation** (optional): `bamCoverage` → Normalized coverage tracks

## ⚙️ Important Notes for RNA-Seq

- **Genome indexing**: Both STAR and RSEM require indexed genomes. The pipeline will check and generate them if missing:
  ```bash
  # STAR index (required in genome directory)
  STAR --runMode genomeGenerate --genomeDir ./star_index --genomeFastaFiles genome.fa --sjdbGTFfile annotation.gtf
  
  # RSEM index (required in genome directory)  
  rsem-prepare-reference --gtf annotation.gtf genome.fa ./rsem_index/reference
  ```

- **Memory requirements**: STAR alignment can be memory-intensive. For large genomes (e.g., human), ensure sufficient RAM (≥30GB recommended)

- **Stranded libraries**: RSEM automatically detects library strandedness; specify with `--strandedness` if needed

## 📈 Output Metrics

The pipeline generates several quality metrics:
- **Fastp reports**: Base quality, GC content, adapter contamination
- **STAR logs**: Mapping rates, unique/multi-mapping reads, splice junctions
- **RSEM statistics**: Alignment rates, gene detection rates
- **BigWig files**: Genome coverage tracks for visualization (IGV, UCSC)

## 📝 Notes

- The genome FASTA and annotation GTF files must be **compatible** (same chromosome names, coordinates)
- For paired-end data, both files must be provided with `-i` and `-I`
- STAR and RSEM indices should be generated once per genome/annotation combination
- Consider using `-r` flag to remove intermediate files (SAM, unsorted BAM) to save disk space

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/Haoyu-Chao/rnaseq-pipeline/issues).

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Haoyu Chao** (haoyuchao@zju.edu.cn)

## 📚 Citation

If you use this pipeline in your research, please cite:

```
@software{rnaseq_pipeline,
  author = {Chao, Haoyu},
  title = {Bulk RNA-Seq Quantification Pipeline},
  year = {2024},
  url = {https://github.com/Haoyu-Chao/rnaseq-pipeline}
}
```

## 🙏 Acknowledgments

- The developers of STAR, RSEM, and all dependency tools
- Zhejiang University for support

---
# Bulk BS-Seq (WGBS) Analysis Pipeline

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Bash](https://img.shields.io/badge/language-bash-blue.svg)](https://www.gnu.org/software/bash/)
[![Bioinformatics](https://img.shields.io/badge/bioinformatics-BS--Seq-purple.svg)](https://en.wikipedia.org/wiki/Bisulfite_sequencing)

A comprehensive Bash pipeline for processing whole-genome bisulfite sequencing (WGBS/BS-Seq) data, from raw reads to methylation calls.

## 📝 Description

This pipeline automates the processing of bisulfite sequencing data, handling various input formats and performing quality control, bisulfite-aware alignment, methylation extraction, and annotation. It uses bwameth for accurate alignment of bisulfite-converted reads and MethylDackel for precise methylation calling.

## ✨ Features

- **Multiple input formats**: Supports SRA, FASTQ (single/paired-end), BAM, and SAM files
- **Bisulfite-aware alignment**: bwameth for accurate mapping of C→T converted reads
- **Methylation extraction**: MethylDackel for per-base methylation levels
- **Quality control**: Fastp for read trimming and quality filtering
- **Duplicate marking**: Sambamba for identifying PCR duplicates
- **Region-specific analysis**: Extract methylation levels for specific genomic regions
- **Resume capability**: Skip completed steps with checkpoint system
- **Clean output**: Optional removal of intermediate files

## 🔧 Dependencies

| Software | Purpose | Installation |
|----------|---------|--------------|
| sra-tools | SRA file conversion | `conda install -c bioconda sra-tools` |
| fastp | Quality control | `conda install -c bioconda fastp` |
| bwameth | Bisulfite-aware alignment | `conda install -c bioconda bwameth` |
| sambamba | BAM processing | `conda install -c bioconda sambamba` |
| samtools | SAM/BAM manipulation | `conda install -c bioconda samtools` |
| MethylDackel | Methylation extraction | `conda install -c bioconda methyldackel` |
| bedtools | Intersection operations | `conda install -c bioconda bedtools` |

## 🚀 Quick Start

```bash
# Clone the repository
git clone https://github.com/Haoyu-Chao/bsseq-pipeline.git
cd bsseq-pipeline

# Make the script executable
chmod +x bsseq-pipeline.sh

# Run the pipeline (single-end FASTQ example)
./bsseq-pipeline.sh -i sample.fq.gz -g genome.fa -a annotation.gff3 -t 8
```

## 📋 Usage

```bash
./bsseq-pipeline.sh -i INPUT -g GENOME -a ANNOTATION [OPTIONS]
```

### Required Parameters

| Option | Description |
|--------|-------------|
| `-i, --input` | Input file (SRA, FASTQ, BAM, or SAM) |
| `-I, --input2` | Second input file for paired-end FASTQ |
| `-g, --genome` | Reference genome FASTA file |
| `-a, --annotation` | Annotation file (GFF3 or GTF format) |

### Optional Parameters

| Option | Description | Default |
|--------|-------------|---------|
| `-q, --mapq` | Minimum mapping quality for BAM filtering | 10 |
| `-o, --output` | Output directory | `./[input]_pipeline_result` |
| `-t, --threads` | Number of CPU threads | 8 |
| `-f, --force` | Force overwrite output directory | No |
| `-r, --remove` | Remove intermediate files | No |
| `-h, --help` | Show help message | - |
| `-v, --version` | Show version | - |

## 📦 Output Structure

```
[input]_pipeline_result/
├── fastq/               # Clean FASTQ files after QC
├── bam/                 
│   ├── alignment/       # Raw aligned BAM files
│   ├── sorted/          # Sorted BAM files
│   ├── dedup/           # Deduplicated BAM files
│   └── filtered/        # Filtered BAM files (by MAPQ)
├── methylation/
│   ├── perBase/         # Per-base methylation calls (BEDGraph)
│   ├── perRegion/       # Regional methylation levels
│   └── CGmap/           # CGmap format files
├── reports/             # QC reports and statistics
└── logs/                # Pipeline execution logs
```

### Methylation Output Files

MethylDackel generates several output formats:
- **BEDGraph**: Per-base methylation levels (`chr start end methylation_level depth`)
- **CGmap**: Complete Genomics methylation format
- **perRegion**: Aggregated methylation over genomic features (promoters, genes, CpG islands)

## 💡 Examples

### 1. Process SRA file
```bash
./bsseq-pipeline.sh -i SRR12345678.sra -g hg38.fa -a hg38.gff3
```

### 2. Paired-end FASTQ files
```bash
./bsseq-pipeline.sh -i sample_R1.fq.gz -I sample_R2.fq.gz -g mm10.fa -a mm10.gtf -t 16
```

### 3. High-stringency mapping
```bash
./bsseq-pipeline.sh -i sample.fq.gz -g genome.fa -a genes.gff3 -q 20 -t 8
```

### 4. Clean up intermediate files
```bash
./bsseq-pipeline.sh -i sample.bam -g genome.fa -a annotation.gff3 -r
```

## 📊 Pipeline Steps

1. **Input conversion** (if SRA): `fasterq-dump` → FASTQ
2. **Quality control**: `fastp` → Clean FASTQ + HTML report
3. **Bisulfite alignment**: `bwameth.py` → SAM
4. **BAM processing**: 
   - `samtools view` → BAM
   - `sambamba sort` → Sorted BAM
   - `sambamba markdup` → Deduplicated BAM
   - `samtools view -q` → Filtered BAM
5. **Methylation extraction**: `MethylDackel extract` → Per-base methylation
6. **Regional methylation**: `MethylDackel mbias` + bedtools → Region-level methylation
7. **Quality reports**: Generate alignment statistics and methylation summary

## ⚙️ Important Notes for BS-Seq

### Genome Preparation
Before running the pipeline, prepare the bwameth index:
```bash
# Convert reference genome for bwameth
bwameth.py index genome.fa
```

### Library Type Considerations
The pipeline automatically detects and handles:
- **Directional libraries**: Standard BS-Seq protocol
- **Non-directional libraries**: Uses appropriate parameters in MethylDackel

### Methylation Contexts
Methylation calls are generated for all contexts (CpG, CHG, CHH) and can be filtered in downstream analysis.

## 📈 Output Metrics

The pipeline generates comprehensive quality metrics:
- **Bisulfite conversion rate**: Estimated from non-CpG contexts or spike-ins
- **Mapping statistics**: Overall alignment rate, unique mapping rate
- **Coverage depth**: Per-base and regional coverage statistics
- **Methylation bias**: Position-specific methylation bias plots
- **CpG coverage**: Genome-wide CpG coverage distribution

## 🔬 Interpretation Notes

- **CpG methylation**: Most relevant for gene regulation studies
- **CHG/CHH methylation**: Important in plants, rare in mammals
- **Coverage thresholds**: Typically require ≥5x coverage for reliable methylation calls
- **Strand-specificity**: MethylDackel maintains strand-specific information

## 📝 Notes

- bwameth requires the reference genome to be indexed with `bwameth.py index`
- For large genomes (e.g., human), ensure sufficient disk space (≥100GB for intermediate files)
- The pipeline maintains strand-specific methylation information throughout
- Consider using `-r` flag for large datasets to manage disk usage

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the [issues page](https://github.com/Haoyu-Chao/bsseq-pipeline/issues).

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Author

**Haoyu Chao** (haoyuchao@zju.edu.cn)

## 📚 Citation

If you use this pipeline in your research, please cite:

```
@software{bsseq_pipeline,
  author = {Chao, Haoyu},
  title = {Bulk BS-Seq (WGBS) Analysis Pipeline},
  year = {2023},
  url = {https://github.com/Haoyu-Chao/bsseq-pipeline}
}
```

## 🙏 Acknowledgments

- The developers of bwameth, MethylDackel, and all dependency tools
- Zhejiang University for support

---

**Last updated**: 2023-03-30


**Last updated**: 2024-11-01
