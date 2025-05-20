# Project-ORF

-----

# Project: Open Reading Frame (ORF) Detection in a Partial Human Transcriptome Assembly

## 1\. Project Goal

The primary objective of this project is to identify Open Reading Frames (ORFs) within a provided partial human transcriptome assembly. This involves several key steps:

  * **ORF Extraction:** Programmatically detect ORFs based on standard start (ATG) and stop codons (TAA, TAG, TGA) from a given FASTA file.
  * **Data Export:** Save the identified ORFs into standard bioinformatics formats: a General Feature Format (GFF) file for genomic annotation and a Multi-FASTA file for the ORF sequences themselves.
  * **Validation with BLAST+:** (Intended) Compare the extracted ORFs against a comprehensive protein database (UniProt/SwissProt) using BLASTp to validate their coding potential and identify actual Coding Sequences (CDS) and Untranslated Regions (UTRs).
  * **Result Analysis and Visualization:** Analyze the characteristics of the predicted ORFs, visualize their length distributions, and assess the performance of the ORF prediction method.
  * **Theoretical Comparison:** Estimate the expected length of ORFs based on random chance (geometric distribution) and compare it to the observed lengths, particularly those intended for validation by BLAST.

## 2\. Prerequisites

To run this project, you need:

  * **Docker:** For setting up a controlled environment with necessary bioinformatics tools (BLAST+).
  * **Python 3.x:** With the `Biopython` and `Matplotlib` libraries installed.
  * **`Homo_sapiens_cdna_assembed.fasta`:** The input transcriptome assembly file.

## 3\. Project Structure

The project is structured with a `Dockerfile` for environment setup and a main Python script (`orf_analyzer.py`) that performs the analysis.

  * **`Dockerfile`**: Configures the Docker image, including installing Python, BLAST+, and downloading the UniProt/SwissProt database.
  * **`orf_analyzer.py`**: The main Python script that contains all the logic for ORF extraction, file generation, BLAST execution/parsing, and analysis.

## 4\. How to Run

1.  **Prepare your Project Directory:**

      * Create a new directory (e.g., `my_orf_project`).
      * Place the `Dockerfile` and `orf_analyzer.py` files (provided in previous steps) inside this directory.
      * Ensure your input FASTA file (`Homo_sapiens_cdna_assembed.fasta`) is also in this directory.

2.  **Build the Docker Image:**
    Open a terminal, navigate to your `my_orf_project` directory, and run:

    ```bash
    docker build -t orf-analyzer:latest .
    ```

    This step will download and install all dependencies, including the large UniProt/SwissProt database, so it may take some time.

3.  **Run the Analysis:**
    Once the image is built, execute the Python script inside the Docker container:

    ```bash
    docker run -v "$(pwd)":/app orf-analyzer:latest python orf_analyzer.py
    ```

    This command mounts your current host directory into the `/app` directory within the container, allowing the script to access input files and write output files directly to your project folder.

## 5\. Results and Analysis

Based on the execution, the following results were obtained:

### 5.1. ORF Extraction

  * **`extracted_orfs.gff`**: A GFF file containing the initial predicted ORFs, specifying their source sequence, coordinates, and reading frame. (Assuming this part ran successfully).
  * **`extracted_orfs.fasta`**: A multi-FASTA file containing the nucleotide sequences of all extracted ORFs. (Assuming this part ran successfully).

### 5.2. ORF Length Distribution

  * **`orf_lengths_histogram.png`**: This plot visualizes the overall distribution of lengths for all predicted ORFs. This helps in understanding the general size range of the identified ORFs. (Assuming this part ran successfully).

### 5.3. BLAST+ Validation - **Issue Encountered**

During the execution of the BLAST-related steps, an error occurred, preventing the validation from completing:

```
Skipping BLAST-related steps due to an error: [Errno 2] No such file or directory: 'blastp'
Please ensure BLAST+ is installed and configured correctly in your Docker environment.
```

Due to this error, the following output files and analyses related to BLAST could **not** be generated or performed:

  * **`blast_results.xml`**: Raw BLASTp output.
  * **`extracted_orfs_with_cds.gff`**: Updated GFF with CDS/UTR annotations.
  * **`orf_true_false_positive_lengths.png`**: Plot comparing validated vs. unvalidated ORF lengths.
  * Summary printout of validated vs. unvalidated ORFs and false positive rate.

This indicates an issue with the `blastp` executable not being found within the Docker container's environment `PATH`, despite the Dockerfile steps intended to install it.

### 5.4. Geometric Distribution Analysis

This step executed successfully:

  * **Expected ORF length (codons, geometric model): 21.33**
  * **Expected ORF length (base pairs, geometric model): 64.00**

## 6\. Discussion

The initial steps of **ORF extraction** and **basic length distribution plotting** appear to have run successfully, providing the foundational `GFF` and `FASTA` files of predicted ORFs. These initial results offer a general overview of the ORFs present in the transcriptome assembly.

The **geometric distribution analysis** provides a theoretical baseline for ORF lengths. The calculated expected length of approximately 64 base pairs represents the average length of an ORF that would occur purely by random chance in a sequence where stop codons appear with equal probability. This theoretical value is crucial for comparison with actual ORFs, as biologically relevant coding sequences are typically much longer, reflecting the evolutionary pressure to avoid premature termination.

However, the critical **BLAST+ validation step could not be completed** due to the `blastp` command not being found. This prevents the project from achieving its full objective of distinguishing truly coding ORFs from those likely generated by random chance. Without BLAST validation, it is impossible to:

  * Confidently identify actual Coding Sequences (CDS) and Untranslated Regions (UTRs).
  * Classify ORFs into "validated" (true positives) and "unvalidated" (potential false positives).
  * Calculate a precise false positive rate for the ORF prediction method.
  * Generate the comparative plot of validated versus unvalidated ORF lengths, which would visually demonstrate whether true ORFs are indeed significantly longer than random ones.

To complete the project's goals and gain full insights into the coding potential of the transcriptome, the issue with the `blastp` executable within the Docker environment needs to be resolved. This typically involves verifying the Dockerfile's `ENV PATH` setting and ensuring that BLAST+ is correctly installed and accessible at the specified path within the container.
