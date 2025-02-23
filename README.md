UNIX Assignment - SNP Data Processing

Overview
This project involves processing SNP genotype data using UNIX command-line tools. The goal is to properly format and organize the dataset for downstream analysis by:

Inspecting and cleaning the data.
Merging SNP genotype and position data.
Extracting genotype data for maize and teosinte.
Sorting chromosomes based on SNP position (increasing and decreasing).
Separating SNPs by chromosome and handling missing data.

Organizing outputs into structured files.
This workflow ensures that the dataset is properly structured for further genetic and evolutionary analysis.

Data Files
Primary Input Files
transposed_genotypes.txt: The primary file used for processing SNP genotype data.
fang_et_al_genotypes.txt: Used only for initial data inspection, providing raw SNP genotype data for maize and teosinte. 
snp_position.txt: Provides the SNP ID, chromosome number, and nucleotide position information.

Output Files
The processing results in 44 structured files categorized into maize and teosinte datasets. The files include sorted SNP data, chromosome-specific datasets, and missing data reports.

Processed Data Files

Intermediate files
transposed_genotypes_header.txt
transposed_genotypes_Newheader.txt
joined_snp_pos_transposed_genotypes.txt

Maize-Specific Files
maize_file.txt
Sortedmaize_file.txt

Increasing Order
updated_Sortedmaize_file.txt

Maize_Increasing_Chromo/
Maize_chromo1.txt to Maize_chromo10.txt
Maize_chromo_multiple.txt
Maize_chromo_unknown.txt

Decreasing Order

Maize_Decreasing_Chromo/
updated_Dash_Sortedmaize_file.txt
updated_Dash_Sorted_Dec_maize_file.txt
Maize_chromo_Dec1.txt to Maize_chromo_Dec10.txt
Maize_chromo_Dec_multiple.txt
Maize_chromo_Dec_unknown.txt

Teosinte-Specific Files
Teosinte_file.txt
Sorted_Incre_Teosinte_file.txt

Increasing Order
updated_Sorted_Incre_Teosinte_file.txt

Teosinte_Increasing_Chromo/
Teosinte_Incre_chromo1.txt to Teosinte_Incre_chromo10.txt
Teosinte_chromo_multiple.txt
Teosinte_chromo_unknown.txt

Decreasing Order

Teosinte_Decreasing_Chromo/
updated_Dash_Sorted_Incre_Teosinte_file.txt
updated_Dash_Sorted_Dec_Teosinte_file.txt
Teosinte_Dec_Chromo1.txt to Teosinte_Dec_Chromo10.txt
Teosinte_Dec_chromo_multiple.txt
Teosinte_Dec_chromo_unknown.txt

Data Processing Workflow

1. Data Preparation and Inspection

Inspection of fang_et_al_genotypes.txt
Used wc to determine the number of lines, words, and characters in the file.
Checked file size with du -h.
Verified the file type using file.
Counted the number of columns using awk -F "	" '{print NF; exit}'.
Checked for missing data (?) using grep -c "?".

Observations:
2783 lines, 2744038 words, and 11051939 characters.
File size: 6.7MB.
986 columns detected.
2782 missing data points (?).

Inspection of snp_position.txt

Used wc to count lines, words, and characters.
Checked file size with du -h.
Verified the file type using file.
Counted the number of columns using awk -F "	" '{print NF; exit}'.
Checked for missing data (?) using grep -c "?".

Observations:
984 lines, 13198 words, and 82763 characters.
File size: 49KB.
15 columns detected.
No missing data (?).


The genotype file was transposed using an awk script to switch rows and columns, making it easier to analyze sample-based data. This file was provided by Dr. Hufford. Headers were adjusted by replacing "Group" with "SNP_ID" for consistency across all files.

The transposed file was merged with snp_position.txt using join, ensuring that each SNP had corresponding chromosome and position information.

File integrity was checked using wc for size validation, awk for data consistency, and vim for manual inspection.

2. Maize Data Processing

Extracting Relevant Columns
Extracted SNP ID, Chromosome, Position, and genotype data specific to maize subgroups:
ZMMIL 
ZMMLR 
ZMMMR 
The filtered dataset was stored in maize_file.txt for further processing.

Sorting and Organizing Data
Increasing Order: SNPs were sorted in ascending order based on position while preserving headers (updated_Sortedmaize_file.txt).

Decreasing Order: Missing data (?) was replaced with - to standardize the format, then SNPs were sorted in descending order (updated_Dash_Sorted_Dec_maize_file.txt).

Separating Data by Chromosome
Data was divided into individual chromosome-specific files (Maize_chromo1.txt to Maize_chromo10.txt).

Two special-case files were created:
Maize_chromo_multiple.txt: SNPs appearing on multiple chromosomes.
Maize_chromo_unknown.txt: SNPs without chromosome assignments.

Verification
wc was used to confirm the expected number of lines.
awk validated column consistency.
vim was used for a final inspection.

3. Teosinte Data Processing

The same workflow was applied to teosinte, extracting relevant genotype groups:
ZMPBA 
ZMPIL 
ZMPJA 
Sorting, chromosome-based separation, and verification steps were identical to maize processing. Files were stored separately for increasing and decreasing order.

The filtered dataset was stored in Teosinte_file.txt for further processing.

Sorting and Organizing Data

Increasing Order: SNPs were sorted in ascending order based on position while preserving headers (updated_Sorted_Incre_Teosinte_file.txt).

Decreasing Order: Missing data (?) was replaced with - to standardize the format, then SNPs were sorted in descending order (updated_Dash_Sorted_Dec_Teosinte_file.txt).

Separating Data by Chromosome

Data was divided into individual chromosome-specific files (Teosinte_Incre_chromo1.txt to Teosinte_Incre_chromo10.txt).

Two special-case files were created:
Teosinte_chromo_multiple.txt: SNPs appearing on multiple chromosomes.
Teosinte_chromo_unknown.txt: SNPs without chromosome assignments.

4. Final Verification
Each generated file underwent multiple checks using:
wc to confirm expected line counts.
awk for field consistency validation.
vim for manual review.

Usage
To reproduce the workflow, execute the UNIX commands sequentially as outlined in the documentation. This ensures proper genotype formatting, structuring, and preparation for downstream genetic analysis.

Notes
Always inspect newly generated files using wc, vim, and awk.
Ensure consistent column formatting across all outputs.


This README provides a comprehensive overview of SNP genotype data processing, ensuring clarity and reproducibility for future analyses.

