# Splice Junction Sequence Logos

This project generates **sequence-logo–style stacks** around splice junctions, separately for **5' (donor)** and **3' (acceptor)** sites. It computes **base frequencies** at each position (–10..+10 bp relative to the splice), converts them to **information content** (bits), and draws stacked letters (A/T/G/C) where each base’s height reflects its contribution. The figure has **two panels**: left = 5' sites, right = 3' sites.

The project supports two modes:

- **BME163 mode:** read pre-extracted sequences from a FASTA (`Splice_Sequences.fasta`).
- **BME263 mode:** read genomic FASTA and splice BED, extract ±10 bp windows per site, then plot.

---

## What the script does

1. **Input parsing**
   - **BME163:** loads junction-centered sequences from `Splice_Sequences.fasta`.
   - **BME263:** 
     - Loads genome from FASTA (Ensembl GRCm38 primary assembly).
     - Normalizes chromosome names (e.g., `1 → chr1`, `MT → chrM`).
     - Reads splice coordinates from a BED (chrom, position, and type in col 4).
     - Extracts **±10 bp** around each junction.
     - **Reverse-complements all 3' acceptor sequences** so that motif orientation is consistent.

2. **5' vs. 3' partition**
   - Splits input windows into **5' (donor)** and **3' (acceptor)** sets based on the type field.
   - Ensures all sequences are aligned positionally from **–10 to +10** relative to the splice point.

3. **Frequency & information content**
   - For each position (0..20), counts A/T/G/C.
   - Converts counts → **relative frequencies**.
   - Computes **information content** \(I\) per position and **base heights**:
     - \( H = -\sum_b p_b \log_2 p_b \)
     - \( I = \log_2(4) - H = 2 - H \) (bits for DNA)
     - Base height at that position: \( p_b \times I \)
   - **Stacks letters top→bottom by frequency** (most frequent on top).

4. **Plotting**
   - Uses provided figure layout:
     ```
     figureWidth = 5; figureHeight = 2
     panelWidth = 1.5; panelHeight = 0.5

     panel1: [0.5/5 , 0.3 , 1.5/5 , 0.5/2]  → 5' logo
     panel2: [2.2/5 , 0.3 , 1.5/5 , 0.5/2] → 3' logo
     ```
   - Draws each stacked column using your letter PNGs (`-A/-T/-G/-C`) scaled to the computed heights.
   - Saves a 2400-DPI PNG.

---

## Usage

### BME263 (genome + BED)
```bash
python3 LastName_FirstName_Assignment_Week5.py \
  -b /path/to/splice_sites.bed \
  -g /path/to/Mus_musculus.GRCm38.dna.primary_assembly.fa \
  -o /path/to/figure.png \
  -A /path/to/A.png -T /path/to/T.png -G /path/to/G.png -C /path/to/C.png
