#UNIX Assignment

##Data Inspection

###Attributes of `fang_et_al_genotypes`


To get number of lines, words, and characters. 

```
$ wc fang_et_al_genotypes.txt

```
The command count the number of lines, words, and characters in ang_et_al_genotypes.txt
wc (word count) is a command that returns:
The number of lines in the file.
The number of words in the file.
The number of characters (bytes) in the file.

```
$ du -h fang_et_al_genotypes.txt
```
The command gets the file size of fang_et_al_genotypes.txt
du (disk usage) displays the size of the file.
-h (human-readable) formats the size in KB, MB, or GB for better readability.

```
$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt
```
The command count the number of columns of fang_et_al_genotypes.txt
awk  is a text-processing tool.
-F "\t"  specifies that the file is tab-separated.
{print NF; exit}  prints the number of fields (columns) in the first row of the file and exits immediately (prevents unnecessary processing).

```
$ file fang_et_al_genotypes.txt
```
The command determine the file type 
The file command analyzes and identifies the type of data stored in the file (e.g., ASCII text, binary, etc.)

```
grep -c "?" fang_et_al_genotypes.txt
```
grep -c counts how many lines contain "?", which may indicate missing data.

By inspecting fang_et_al_genotypes.txt file learned that:

1. There are 2783 lines, 2744038 words, and 11051939 characters
2. The sizes is 6.7M
3. The file is ascii text with very long lines
4. There are 986 columns
5. There was 2782 ?s 


###Attributes of `snp_position.txt`
```
$ wc snp_position.txt 
```
The command returns the total lines, words, and characters in the file.

```
$ du -h snp_position.txt
```
This command displays the file size in a human-readable format (e.g., KB, MB).

```
$ file snp_position.txt
```
This code identifies whether the file is ASCII text, binary, or another format.

```
$ awk -F "\t" '{print NF; exit}' snp_position.txt
```
The command counts the number of columns (fields) in the first row.
 
 ````
 grep -c "?" snp_position.txt
 ````
 grep -c counts how many lines contain "?", which may indicate missing data.

By inspecting this snp_position.txt  file learned that:

1. There are 984 lines, 13198 words, and 82763 characters.
2. The size is 49K 
3. The file is ASCII text
4. There are 15 columns.
5. There was 0 ?s 2782



##Data Processing

```
tail -n +3 transposed_genotypes.txt > transposed_genotypes_header.txt
```
This code extracts all lines from transposed_genotypes.txt, starting from the third line (skipping the first two lines).
And redirects the output to a new file transposed_genotypes_header.txt.
The reason was to remove the first two lines from transposed_genotypes.txt, with assumption that they are unnecessary headers for the further processing.
 ```
 vim transposed_genotypes_header.txt
 :set nowrap 
 :q!
 ```
The code above Opens transposed_genotypes_header.txt in the Vim text editor.
The set nowrap command makes the lines will stay on a single line and require horizontal scrolling to view the column headers.
Vim mode was exited without saving any changes that might occurred unknowingly
This allows for manual inspection the file before further processing.

```
sed 's/Group/SNP_ID/g' transposed_genotypes_header.txt > transposed_genotypes_Newheader.txt
```
Here,sed (stream editor) was used to replace all occurrences of the word "Group" with "SNP_ID".
The modified content is saved into a new file transposed_genotypes_Newheader.txt.
The idea was to rename the "Group" column/header to "SNP_ID" for consistency in downstream analysis.
Because the Group column "transposed_genotypes.txt"is actually SNP_IDs. which is common to SNP_ID column of "snp_position.txt" file.
 
```
wc transposed_genotypes_Newheader.txt
vim transposed_genotypes_Newheader.txt
:set nowrap
:q!
 ```
This was to inspect the newly created file to ensure there is no data lost, by checking the size of the file (wc).
Then, viewing it in Vim with no line wrapping to keep each row in a single line.
Finally, exiting without making changes.

```
join -1 1 -2 1 -t $'\t' snp_position.txt transposed_genotypes_Newheader.txt > joined_snp_pos_transposed_genotypes.txt

```
This command uses join to merge two tab-separated files "snp_position.txt" and "transposed_genotypes_Newheader.txt" based on the first column of each file (SNP positions) and transposed_genotypes_Newheader.txt (genotype data) are joined using their first columns as keys.
The merged output is saved in joined_snp_pos_transposed_genotypes.txt.

Breakdown of the explanation of the code. 
"join" combines two files by matching a common field (column).
"-1 1" specifies that the first column in snp_position.txt should be used as the key for joining.
"-2 1" specifies that the first column in transposed_genotypes_Newheader.txt should be used as the key for joining.
"-t $'\t'" specifies that the files are tab-separated.
"snp_position.txt" fhe first input file containing SNP positions.
transposed_genotypes_Newheader.txt: the second input file containing genotype data with a modified header.
"> joined_snp_pos_transposed_genotypes.txt" redirects the output (merged data) into a new file "joined_snp_pos_transposed_genotypes.txt".
Merging SNP position data (snp_position.txt) with genotype data (transposed_genotypes_Newheader.txt) ensures that both files are joined based on their first column (likely containing SNP IDs).
Produces a combined file (joined_snp_pos_transposed_genotypes.txt) for further extraction of the relavant columns.
 
```
w -l joined_snp_pos_transposed_genotypes.txt 
wc joined_snp_pos_transposed_genotypes.txt
vim joined_snp_pos_transposed_genotypes.txt
:set nowrap
:q!
 ```
This was to inspect the newly created file to ensure there is no data lost, by checking the size of the file (wc).
Then, viewing it in Vim with no line wrapping to keep each row in a single line.
Finally, exiting without making changes.

###Maize Data

```
mkdir Maize
```
Creates a new directory named Maize.
This to organize files peculiar to maize by placing them into a dedicated Maize folder.


```
cp -rf joined_snp_pos_transposed_genotypes.txt Maize
```
Copies the processed joined file into the maize folder.
Detail breakdown
"cp"Copies files.
"-r:" Recursively copies directories (not necessary for a single file but harmless).
"-f:" Forces the copy by overwriting any existing file with the same name.



```
cd Maize
```
Changes the working directory to Maize.
I Moved into the newly created directory to work with the copied file. That way i can have original file intact for other analysis Teosinte.

```
awk '{for(i=1;i<=NF;i++) if ($i == "ZMMIL") print "Found at column " i}' joined_snp_pos_transposed_genotypes.txt

```
The code finds the exact column(s) where "ZMMIL" appears in joined_snp_pos_transposed_genotypes.txt.
Breakdown of code
"awk" is a text-processing tool used for pattern scanning and processing.
"NF" Number of fields (columns) in each row.
"for(i=1; i<=NF; i++)"  loops through all columns in the row.
"if ($i == "ZMMIL")"" checks if the current column contains "ZMMIL".
"print "Found at column " i"" Prints the column number where "ZMMIL" appears.

The purpose was to find the column(s) in "ZMMIL", notice the where is first found and last found.


```
awk '{for(i=1;i<=NF;i++) if ($i == "ZMMLR") print "Found at column " i}' joined_snp_pos_transposed_genotypes.txt

```
This command is exactly finds and printing the column in "ZMMLR" just as the previous command was doing for "ZMMIL"


```
awk '{for(i=1;i<=NF;i++) if ($i == "ZMMMR") print "Found at column " i}' joined_snp_pos_transposed_genotypes.txt

```
This command does the same operation as the previous two commands but specifically for "ZMMMR".
It helps identify where this specific string occurs within the joined_snp_pos_transposed_genotypes.txt, similar to how the commands searched for "ZMMIL" and "ZMMLR".

```
here is my snippet of code used for data processing
```
Counts how many times "ZMMIL" appears in joined_snp_pos_transposed_genotypes.txt.
It prints the total count of occurrences of "ZMMIL".
Breakdown of the code 
"awk" a text-processing tool.
"{for(i=1;i<=NF;i++) if($i == "ZMMIL") count++}" loops through all columns (i=1 to NF, which is the number of fields).
If a field ($i) contains "ZMMIL", it increments the count variable.
"END{print count}" after processing the entire file, it prints the total count of "ZMMIL" occurrences.

This helps to verify whether the expected number of occurrences is present.

```
awk '{for(i=1;i<=NF;i++) if($i == "ZMMLR") count++}
END{print count}' joined_snp_pos_transposed_genotypes.txt

```
Similar to the previous command, this scans the file joined_snp_pos_transposed_genotypes.txt, searches for occurrences of "ZMMLR" across all columns in all rows, and prints the total count of matches.
After processing all lines, it prints the total count of occurrences of "ZMMLR" in the entire file.
```
awk '{for(i=1;i<=NF;i++) if($i == "ZMMMR") count++}
END{print count}' joined_snp_pos_transposed_genotypes.txt

```
This command does the same operation as the previous ones but specifically for "ZMMMR".
The script scans the entire file and counts how many times "ZMMMR" appears, then prints the total count.
It helps identify where this specific string occurs within the dataset, similar to how the commands searched for "ZMMIL" and "ZMMLR".
 
 
```
cut -d $'\t' -f1,3,4,2508-2797,1225-2480,2481-2507 joined_snp_pos_transposed_genotypes.txt > maize_file.txt

```
This command extracts specific columns from joined_snp_pos_transposed_genotypes.txt and saves the output to maize_file.txt.

Breakdown:
"cut" a command used to extract specific columns from a file.
" -d $'\t' " specifies the delimiter as a tab (\t), meaning the file is tab-separated.
"-f1,3,4,2508-2797,1225-2480,2481-2507" selects specific fields (columns) from the input file:
Column 1 (SNP_ID)
Column 3 (Chromosome)
Column 4 (Position)
Columns 2508 to 2797 (ZMMIL)
Columns 1225 to 2480 (ZMMLR)
Columns 2481 to 2507 (ZMMMR)
"joined_snp_pos_transposed_genotypes.txt" is the input file.
"> maize_file.txt" redirects the extracted columns into maize_file.txt.

The idea is to This script extracts selected columns from the tab-separated file joined_snp_pos_transposed_genotypes.txt and saves them into maize_file.txt. 
This is to ensure that only relevant columns are included in the new file.

```
tail -n +6 maize_file.txt  | awk -F "\t" '{print NF; exit}'
```
This command does the same operation as the previous ones but specifically for file inspection.
The script skips the first 5 lines of maize_file.txt, then counts and prints the number of columns (fields) in the first remaining line.
It helps verify the structure of the file by checking how many columns are present.

```
wc maize_file.txt
```
This command does the same operation as the previous ones but specifically for file inspection.
The script counts and prints the number of lines, words, and bytes in maize_file.txt.
It helps verify the file's size and structure by providing basic statistics.
```
vim maize_file.txt

```
```
:set nowrap 

```
```
:q!
```
The script opens the file in Vim, disables line wrapping, and then exits without saving changes.
It helps inspect the file while ensuring that long lines are displayed in a single continuous line rather than wrapping to the next line.

Note: Any time i created a new output file  do ;
"
tail -n +6 maize_file.txt  | awk -F "\t" '{print NF; exit}'
wc maize_file.txt 
vim maize_file.txt
:set nowrap 
:q!
". Hence i will no longer explain in again. 

```
sort -k3,3n maize_file.txt > Sortedmaize_file.txt
```
This command sorts the file maize_file.txt based on the third column in numerical order and saves the output to Sortedmaize_file.txt.
This script sorts maize_file.txt by the third column(SNP Position) in ascending numerical order and saves the sorted version as Sortedmaize_file.txt. 
It helps organize the data based on values in the third column.
But it mess up the header line too.

```
wc Sortedmaize_file.txt 

```

```
vim Sortedmaize_file.txt
:set nowrap 
:q!
```

```
tail -n +6 Sortedmaize_file.txt  | awk -F "\t" '{print NF; exit}'

```
```
(head -n 1 maize_file.txt && tail -n +2 maize_file.txt | sort -k3,3n) > updated_Sortedmaize_file.txt

```
This script keeps the header intact while sorting the rest of the file by the third column in ascending numerical order.
The sorted version is saved as updated_Sortedmaize_file.txt, ensuring that column labels remain at the top.

```
tail -n +6 updated_Sortedmaize_file.txt  | awk -F "\t" '{print NF; exit}'
```

```
wc  updated_Sortedmaize_file.txt 
```
```
vim updated_Sortedmaize_file.txt
:set nowrap 
:q!
```

```
grep -v "^#" updated_Sortedmaize_file.txt | cut -f2 | sort | uniq -c
```
This command does the same operation as the previous ones but specifically for checking if individual chromosomes are extracted correctly.
The script removes commented lines, extracts the second column (which contains chromosome Numbers), sorts it, and counts unique occurrences.
It helps verify the occurrence of each chromosomes extracted correctly in the upcoming step.

```
mkdir Maize_Increasing_Chromo  
```
This script creates a directory named Maize_Increasing_Chromo, which can be used to store files related to increasing chromosome order

```
cp -rf updated_Sortedmaize_file.txt Maize_Increasing_Chromo
```

This command copies the file updated_Sortedmaize_file.txt into the location Maize_Increasing_Chromo, either as a new file or inside a directory, replacing any existing file with the same name.

```
cd Maize_Increasing_Chromo 
```

This command is used to navigate into the directory named Maize_Increasing_Chromo, this to allow me to work inside it.

```
for i in {1..10}; do
    out_file="Maize_chromo${i}.txt"
    { head -n 1 updated_Sortedmaize_file.txt && awk -v chr="$i" '$2 == chr' updated_Sortedmaize_file.txt; } > "$out_file"
    echo "Chromosome $i: $(wc -l < "$out_file") lines"
done
```
The script processes a file (updated_Sortedmaize_file.txt) and extracts data for each chromosome (1 to 10), creating separate files for each.
The code loops through chromosome numbers 1 to 10;
creates a separate file (Maize_chromo1.txt, Maize_chromo2.txt, etc.) for each chromosome;
copies the header (first line) from the original file;
extracts rows where the second column matches the current chromosome number;
saves the extracted data into the respective chromosome file;
displays the number of lines in each output file for verification.


```
for i in {1..10}; do
    file="Maize_chromo${i}.txt"
    wc "$file"
done

```
```
tail -n +6 Maize_chromo1.txt  | awk -F "\t" '{print NF; exit}'
```

```
wc Maize_chromo1.txt 
```

This script iterates through chromosome numbers 1 to 10 and counts the number of lines, words, and characters in each file.
This provides basic statistics on the size of each chromosome file which is useful for verifying data extraction and ensuring files contain expected data.

```
{ head -n 1 updated_Sortedmaize_file.txt && awk '$2 == "multiple"' updated_Sortedmaize_file.txt; } > Maize_chromo_multiple.txt

```
This command extracts rows where the second column contains the word "multiple" and saves them into a new file (Maize_chromo_multiple.txt) while keeping the header.
The idea is to separate entries labeled as "multiple" in column 2 into Maize_chromo_multiple.txt.
The command maintained the header, ensuring data structure remains intact.

```
wc Maize_chromo_multiple.txt
```

```
{ head -n 1 updated_Sortedmaize_file.txt && awk '$2 == "unknown"' updated_Sortedmaize_file.txt; } > Maize_chromo_unknown.txt
```
This command extracts rows where the second column contains the word "unknown" and saves them into a new file while keeping the header.
Just like for multiple, This command extracts rows where the second column contains the word "unknown" and saves them into a new file (Maize_chromo_unknown.txt) while keeping the header.
The idea is to separate entries labeled as "multiple" in column 2 into Maize_chromo_unknown.txt
The command maintained the header, ensuring data structure remains intact.

```
wc Maize_chromo_unknown.txt
```

```
vim Maize_chromo1.txt 
:set nowrap 
:q!
```
Note: All the files created were vim into to check if everything looks good.

```
cd ../ ### back to the Maize folder 
```

```
mkdir Maize_Decreasing_Chromo 
```
creating a new directory (Maize_Decreasing_Chromo) to help organize files for chromosomes based on decreasing SNP position
```
cp -rf updated_Sortedmaize_file.txt Maize_Decreasing_Chromo 
```
coping  updated_Sortedmaize_file.txt into Maize_Decreasing_Chromo for further processing and extraction of the files.


```
here is my snippet of code used for data processing
```
This command uses sed to replace all occurrences of the question mark (?) with a dash (-) in the file updated_Sortedmaize_file.txt and saves the result to a new file(updated_Dash_Sortedmaize_file.txt).
This command modifies the original file by replacing all ? characters with - and creates a new file to preserve the original content.

```
tail -n +6 updated_Dash_Sortedmaize_file.txt | awk -F "\t" '{print NF; exit}'
wc -l updated_Dash_Sortedmaize_file.txt
wc updated_Dash_Sortedmaize_file.txt
vim updated_Dash_Sortedmaize_file.txt
:set nowrap 
:q!
```
These commands were used to inspect the new file created again.
```
(head -n 1 updated_Dash_Sortedmaize_file.txt && tail -n +2 updated_Dash_Sortedmaize_file.txt | sort -k3,3nr) > updated_Dash_Sorted_Dec_maize_file.txt

```
This command creates a new file (updated_Dash_Sorted_Dec_maize_file.txt) that retains the header from the original file.
It sorts the remaining data based on the third column (Position) in numerical descending order.

```
tail -n +6 updated_Dash_Sorted_Dec_maize_file.txt | awk -F "\t" '{print NF; exit}'
wc -l updated_Dash_Sorted_Dec_maize_file.txt
wc updated_Dash_Sorted_Dec_maize_file.txt
vim updated_Dash_Sorted_Dec_maize_file.txt
:set nowrap 
:q!
```
The new file was inspected as usual. 


```
for i in {1..10}; do
    out_file="Maize_chromo_Dec${i}.txt"
    { head -n 1 updated_Dash_Sorted_Dec_maize_file.txt && awk -v chr="$i" '$2 == chr' updated_Dash_Sorted_Dec_maize_file.txt; } > "$out_file"
    echo "Chromosome $i: $(wc -l < "$out_file") lines"
done

```
This similar to what was done for files in increasing order.
Separates data by chromosome from updated_Dash_Sorted_Dec_maize_file.txt into 10 individual files (Maize_chromo_Dec1.txt to Maize_chromo_Dec10.txt).
Each output file retains the original header for clarity and organization.
Provides a count of the number of lines in each chromosome file for verification.


```
for i in {1..10}; do
    file="Maize_chromo_Dec${i}.txt"
    wc "$file"
done
```
This Bash script counts the number of lines, words, and characters in each of the chromosome files (Maize_chromo_Dec1.txt to Maize_chromo_Dec10.txt).


```
{ head -n 1 updated_Dash_Sorted_Dec_maize_file.txt && awk '$2 == "multiple"' updated_Dash_Sorted_Dec_maize_file.txt; } > Maize_chromo_Dec_multiple.txt
```

```
wc -l Maize_chromo_Dec_multiple.txt
```
```
wc Maize_chromo_Dec_multiple.txt
```
This command counts the number of lines, words, and characters in each of the files of multiple  positions.


```
{ head -n 1 updated_Dash_Sorted_Dec_maize_file.txt && awk '$2 == "unknown"' updated_Dash_Sorted_Dec_maize_file.txt; } > Maize_chromo_Dec_unknown.txt
```

```
wc -l Maize_chromo_Dec_unknown.txt
```

```
wc Maize_chromo_Dec_unknown.txt
```
This command counts the number of lines, words, and characters in each of the files of unknown  positions .


```
vim Maize_chromo_Dec1.txt
:set nowrap 
:q!
```
This was done for all the newly created files.

###Teosinte Data

All the Command where already explained above hence will not be explained here.
The codes below were just came a brief modification in file names and  the  for Teosinte groups (ZMPBA, ZMPIL, and ZMPJA).
So it was only the same commands use in Maize that were modified according without changes in idea or purpose.

```
cd /home/lkgbeda/Documents/BCB546_Spring2025/assignments/Trial/UNIX_Assignment ###
cd Teosinte
cp -rf joined_snp_pos_transposed_genotypes.txt Teosinte
cd Teosinte

awk '{for(i=1;i<=NF;i++) if ($i == "ZMPBA") print "Found at column " i}' joined_snp_pos_transposed_genotypes.txt

awk '{for(i=1;i<=NF;i++) if ($i == "ZMPIL") print "Found at column " i}' joined_snp_pos_transposed_genotypes.txt

awk '{for(i=1;i<=NF;i++) if ($i == "ZMPJA") print "Found at column " i}' joined_snp_pos_transposed_genotypes.txt


awk '{for(i=1;i<=NF;i++) if($i == "ZMPBA") count++}
END{print count}' joined_snp_pos_transposed_genotypes.txt

awk '{for(i=1;i<=NF;i++) if($i == "ZMPIL") count++}
END{print count}' joined_snp_pos_transposed_genotypes.txt

awk '{for(i=1;i<=NF;i++) if($i == "ZMPJA") count++}
END{print count}' joined_snp_pos_transposed_genotypes.txt

cut -d $'\t' -f1,3,4,89-988,1178-1218,989-1022 joined_snp_pos_transposed_genotypes.txt > Teosinte_file.txt
vim Teosinte_file.txt
:set nowrap 

tail -n +6 Teosinte_file.txt | awk -F "\t" '{print NF; exit}'

sort -k3,3n Teosinte_file.txt > Sorted_Incre_Teosinte_file.txt

tail -n +6 Sorted_Incre_Teosinte_file.txt | awk -F "\t" '{print NF; exit}'

wc Sorted_Incre_Teosinte_file.txt 
vim Sorted_Incre_Teosinte_file.txt
:set nowrap 

(head -n 1 Teosinte_file.txt && tail -n +2 Teosinte_file.txt | sort -k3,3n) > updated_Sorted_Incre_Teosinte_file.txt

tail -n +6 updated_Sorted_Incre_Teosinte_file.txt | awk -F "\t" '{print NF; exit}'

wc updated_Sorted_Incre_Teosinte_file.txt 
vim updated_Sorted_Incre_Teosinte_file.txt
:set nowrap 
:q!
grep -v "^#" updated_Sorted_Incre_Teosinte_file.txt | cut -f2 | sort | uniq -c


cp -rf updated_Sorted_Incre_Teosinte_file.txt Teosinte_Increasing_Chromo

cd Teosinte_Increasing_Chromo

#mkdir -p Teosinte_Increasing_Chromo  # Ensure the directory exists

{ head -n 1 updated_Sortedmaize_file.txt && awk '$2 == 1' updated_Sortedmaize_file.txt; } > Teosinte_Incre_chromo1.txt

for i in {1..10}; do
    out_file="Teosinte_Incre_chromo${i}.txt"
    { head -n 1 updated_Sorted_Incre_Teosinte_file.txt && awk -v chr="$i" '$2 == chr' updated_Sorted_Incre_Teosinte_file.txt; } > "$out_file"
    echo "Chromosome $i: $(wc -l < "$out_file") lines"
done

for i in {1..10}; do
    file="Teosinte_Incre_chromo${i}.txt"
    wc "$file"
done

vim Teosinte_Incre_chromo1.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo2.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo3.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo4.txt
:set nowrap 
:q!
vim Teosinte_Incre_chromo5.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo6.txt
:set nowrap 
:q!
vim Teosinte_Incre_chromo7.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo8.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo9.txt
:set nowrap 
:q!

vim Teosinte_Incre_chromo10.txt
:set nowrap 
:q!


{ head -n 1 updated_Sorted_Incre_Teosinte_file.txt && awk '$2 == "multiple"' updated_Sorted_Incre_Teosinte_file.txt; } > Teosinte_chromo_multiple.txt

tail -n +6 Teosinte_chromo_multiple.txt | awk -F "\t" '{print NF; exit}'
wc -l Teosinte_chromo_multiple.txt
wc Teosinte_chromo_multiple.txt
vim Teosinte_chromo_multiple.txt
:set nowrap 
:q!


{ head -n 1 updated_Sorted_Incre_Teosinte_file.txt && awk '$2 == "unknown"' updated_Sorted_Incre_Teosinte_file.txt; } > Teosinte_chromo_unknown.txt

wc -l Teosinte_chromo_unknown.txt
wc Teosinte_chromo_unknown.txt
vim Teosinte_chromo_unknown.txt
:set nowrap 
:q!


## Decreasing

cp -rf updated_Sorted_Incre_Teosinte_file.txt  Teosinte_Dereasing_Chromo 

cd Teosinte_Dereasing_Chromo 

sed 's/?/-/g' updated_Sorted_Incre_Teosinte_file.txt > updated_Dash_Sorted_Incre_Teosinte_file.txt

tail -n +6 updated_Sorted_Incre_Teosinte_file.txt | awk -F "\t" '{print NF; exit}'
wc -l updated_Dash_Sorted_Incre_Teosinte_file.txt
wc updated_Dash_Sorted_Incre_Teosinte_file.txt
vim updated_Dash_Sorted_Incre_Teosinte_file.txt
:set nowrap 
:q!

 
(head -n 1 updated_Sorted_Incre_Teosinte_file.txt && tail -n +2 updated_Sorted_Incre_Teosinte_file.txt | sort -k3,3nr) > updated_Dash_Sorted_Dec_Teosinte_file.txt

tail -n +6 updated_Dash_Sorted_Dec_Teosinte_file.txt | awk -F "\t" '{print NF; exit}'
wc -l updated_Dash_Sorted_Dec_Teosinte_file.txt
wc updated_Dash_Sorted_Dec_Teosinte_file.txt
vim updated_Dash_Sorted_Dec_Teosinte_file.txt
:set nowrap 
:q!

mkdir -p Teosinte_Increasing_Chromo
  # Ensure the directory exists


cd Teosinte_Dereasing_Chromo

#mkdir -p Teosinte_Dereasing_Chromo  # Ensure the directory exists

Teosinte_Decre_Chromo

for i in {1..10}; do
    out_file="Teosinte_Dec_Chromo${i}.txt"
    { head -n 1 updated_Dash_Sorted_Dec_Teosinte_file.txt && awk -v chr="$i" '$2 == chr' updated_Dash_Sorted_Dec_Teosinte_file.txt; } > "$out_file"
    echo "Chromosome $i: $(wc -l < "$out_file") lines"
done

for i in {1..10}; do
    file="Teosinte_Dec_Chromo${i}.txt"
    wc "$file"
done


vim Teosinte_Dec_Chromo1.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo2.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo3.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo4.txt
:set nowrap 
:q!
vim Teosinte_Dec_Chromo5.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo6.txt
:set nowrap 
:q!
vim Teosinte_Dec_Chromo7.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo8.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo9.txt
:set nowrap 
:q!

vim Teosinte_Dec_Chromo10.txt
:set nowrap 
:q!


{ head -n 1 updated_Sorted_Incre_Teosinte_file.txt && awk '$2 == "multiple"' updated_Sorted_Incre_Teosinte_file.txt; } > Teosinte_Dec_chromo_multiple.txt

tail -n +6 Teosinte_chromo_multiple.txt | awk -F "\t" '{print NF; exit}'
wc -l Teosinte_Dec_chromo_multiple.txt
wc Teosinte_Dec_chromo_multiple.txt
vim Teosinte_Dec_chromo_multiple.txt
:set nowrap 
:q!


{ head -n 1 updated_Sorted_Incre_Teosinte_file.txt && awk '$2 == "unknown"' updated_Sorted_Incre_Teosinte_file.txt; } > Teosinte_Dec_chromo_unknown.txt

wc -l Teosinte_Dec_chromo_unknown.txt
wc Teosinte_Dec_chromo_unknown.txt
vim Teosinte_Dec_chromo_unknown.txt
:set nowrap 
:q!

```

The processing steps for Teosinte data follow the same workflow as Maize, with modifications specific to Teosinte groups and column selections.
Below is an overview of the steps applied:
1. Data Preparation
Copied the processed genotype data (joined_snp_pos_transposed_genotypes.txt) into the Teosinte directory.
Identified the relevant columns for Teosinte groups:
ZMPBA
ZMPIL
ZMPJA
Located and counted occurrences of these groups within the dataset using awk.
2. Extracting Relevant Columns
Extracted SNP ID, Chromosome, Position, and genotype data for Teosinte groups using the cut command.
Saved the output as Teosinte_file.txt.
Verified the structure by checking the number of columns and inspecting the file in vim.
3. Sorting by Chromosome Position
Ascending Order:
Sorted Teosinte_file.txt by SNP position.
Ensured the header remained intact during sorting.
Saved as updated_Sorted_Incre_Teosinte_file.txt.
Descending Order:
Replaced missing data (? with -).
Sorted SNPs in descending order by position.
Saved as updated_Dash_Sorted_Dec_Teosinte_file.txt.
4. Separating Data by Chromosome
Increasing Order:
Created Teosinte_Increasing_Chromo directory.
Generated individual files for each chromosome (Teosinte_Incre_chromo1.txt to Teosinte_Incre_chromo10.txt).
Decreasing Order:
Created Teosinte_Decreasing_Chromo directory.
Extracted chromosome-specific SNPs into files (Teosinte_Dec_Chromo1.txt to Teosinte_Dec_Chromo10.txt).
5. Extracting Special Cases
SNPs with multiple positions:
Extracted into Teosinte_chromo_multiple.txt (Increasing Order).
Extracted into Teosinte_Dec_chromo_multiple.txt (Decreasing Order).
SNPs with unknown positions:
Extracted into Teosinte_chromo_unknown.txt (Increasing Order).
Extracted into Teosinte_Dec_chromo_unknown.txt (Decreasing Order).
6. Final Verification
Inspected all generated files using wc, awk, and vim to confirm structure and content integrity.
