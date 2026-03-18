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

**Last updated**: 2024-11-01
