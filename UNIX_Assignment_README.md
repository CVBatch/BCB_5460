# UNIX Assignment

## Data Inspection

### Attributes of `fang_et_al_genotypes`

Determining attributes of file:
```bash
#number of rows in the file and how spacing affect the line count
$ grep -c "[^ \\n\\t]" fang_et_al_genotypes.txt

#file size in readable text
$ ls -lh fang_et_al_genotypes.txt

#number of columns in the file
$ awk -F "\t" '{print NF; exit}' fang_et_al_genotypes.txt

#confirming the number of columns at the beginning of the file match the number of columns at the end of the file
$ tail -n +5 fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'
```
By inspecting this file I learned the file:

1. contains 2783 rows
2. is 11 megabytes in size
3. contains 986 columns



### Attributes of `snp_position.txt`

Determining attributes of file:
```bash
#determining the number of rows in the file and how spacing affect the line count
$ grep -c "[^ \\n\\t]" snp_position.txt

#determining the file size in readable text
$ ls -lh snp_position.txt

#determining the number of columns in the file
$ awk -F "\t" '{print NF; exit}' snp_position.txt

#confirming the number of columns at the beginning of the file match the number of columns at the end of the file
$ tail -n +5 snp_position.txt | awk -F "\t" '{print NF; exit}'
```
By inspecting this file I learned the file:

1. contains 984 rows
2. is 81 kilobytes in size
3. contains 15 columns


## Data Processing

Before extracting the Maize and Teosinte data
```bash
#make a different directory to house the homework files
$ mkdir UNIX_HW

#copy the data files into the new directory to avoid altering the original files
$ cp fang_et_al_genotypes.txt UNIX_HW

$ cp snp_position.txt UNIX_HW

$ cp transpose.awk UNIX_HW

$ cp transposed_genotypes.txt

```
### Maize Data

Move the maize data over to one folder and add heading from fang_et_al_genotypes.txt
```bash
$ grep -E "ZMMIL|ZMMLR|ZMMMR" fang_et_al_genotypes.txt > maize_data.txt

#saving heading from fang_et_al
$ head -n 1 fang_et_al_genotypes.txt > fang_et_al_heading.txt

#adding the heading to maize data
$ cat fang_et_al_heading.txt maize_data.txt > complete_maize_data.txt
```
Transpose the data
```bash
$ awk -f transpose.awk complete_maize_data.txt > transposed_maize_genotypes.txt
```
Remove the heading and save the heading in a separate file
```bash
$ tail -n +4 transposed_maize_genotypes.txt > headless_maize.txt

$ head -n 3 transposed_maize_genotypes.txt > transposed_maize_heading.txt
```
Trim the snp data, remove the heading and save the heading in a separate file
```bash
$ cut -f 1,3,4 snp_position.txt > trimmed_snp.txt

$ tail -n +2 trimmed_snp.txt > headless_snp_position.txt

$ head -n 1 trimmed_snp.txt > snp_heading.txt
```
Sort the files
```bash
$ sort -k1,1 headless_maize.txt > sorted_headless_maize.txt

$ sort -k1,1 headless_snp_position.txt > sorted_headless_snp.txt
```
Join ```sorted_headless_snp.txt``` and ```sorted_headless_maize.txt```based on the first column of each file, with the resulting file delimited by tabs
```bash
$ join -1 1 -2 1 -t $'\t' sorted_headless_snp.txt sorted_headless_maize.txt > combined_maize_data.txt
```
Make a file with all the unknown snp positions
```bash
$ grep "unknown" combined_maize_data.txt > unknown_position_maize.txt
```
Make a file with all the snp that have multiple positions
```bash
$  grep "multiple" combined_maize_data.txt > multiple_position_maize.txt
```

Make a file without the unknown and multiple positions
```bash
$ grep -vE "unknown|multiple" combined_maize_data.txt > trimmed_maize.txt
```
Make a file containing the data for chromosome 1 and repeat for the other nine chromosomes
```bash
awk '$2 == "1" {print}' combined_maize_data.txt > chromosome1_maize.txt
```
Sort the snps based on increasing position values; repeat with the other chromosome files
```bash
$ sort -k3,3 -n  chromosome1_maize.txt > sorted_chromosome1_maize.txt
``` 
Sort the snps based on decreasing position values and replace missing data with -; repeat with the other chromosome files
```bash
$ sort -k3,3 -n -r chromosome1_maize.txt > sr_chromosome1_maize.txt

$ sed 's/?/-/g' sr_chromosome1_maize.txt > ssr_chromosome1_maize.txt
```
Add the snp heading back back to all of the files
```bash
$ cat snp_heading.txt sorted_chromosome_maize.txt > final_sorted_chromosome_maize.txt
```

### Teosinte Data

Move the teosinte data over to one folder and add heading from fang_et_al_genotypes.txt
```bash
$ grep -E "ZMPBA|ZMPIL|ZMPJA" fang_et_al_genotypes.txt > teosinte_data.txt

#adding the fang_et_al heading to teosinte data
$ cat fang_et_al_heading.txt teosinte_data.txt > complete_teosinte_data.txt
```

Transpose the data
```bash
$ awk -f transpose.awk complete_teosinte_data.txt > transposed_teosinte_genotypes.txt
```
 
Remove the heading and save the heading in a separate file
```bash
$ tail -n +4 transposed_teosinte_genotypes.txt > headless_teosinte.txt

$ head -n 3 transposed_teosinte_genotypes.txt > transposed_teosinte_heading.txt
```
Sort the file
```bash
$ sort -k1,1 headless_teosinte.txt > sorted_headless_teosinte.txt
```
Join ```sorted_headless_snp.txt``` and ```sorted_headless_teosinte.txt``` based on the first column of each file, with the resulting file delimited by tabs
```bash
$ join -1 1 -2 1 -t $'\t' sorted_headless_snp.txt sorted_headless_teosinte.txt > combined_teosinte_data.txt
```
Make a file with all the unknown snp positions
```bash
$ grep "unknown" combined_teosinte_data.txt > unknown_position_teosinte.txt
```
Make a file with all the snp that have multiple positions
```bash
$  grep "multiple" combined_teosinte_data.txt > multiple_position_teosinte.txt
```
Make a file without the unknown and multiple positions
```bash
$ grep -vE "unknown|multiple" combined_teosinte_data.txt > trimmed_teosinte.txt
```
Make a file containing the data for chromosome 1 and repeat for the other nine chromosomes
```bash
awk '$2 == "1" {print}' combined_teosinte_data.txt > chromosome1_teosinte.txt
```
Sort the snps based on increasing position values; repeat with the other chromosome files
```bash
$ sort -k3,3 -n  chromosome1_teosinte.txt > sorted_chromosome1_teosinte.txt
``` 
Sort the snps based on decreasing position values and replace missing data with -; repeat with the other chromosome files
```bash
$ sort -k3,3 -n -r chromosome1_teosinte.txt > sr_chromosome1_teosinte.txt

$ sed 's/?/-/g' sr_chromosome1_teosinte.txt > ssr_chromosome1_teosinte.txt
```
Add the snp heading back back to all of the files
```bash
$ cat snp_heading.txt sorted_chromosome_teosinte.txt > final_sorted_chromosome_teosinte.txt
```
