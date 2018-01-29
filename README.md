# assignment-1

# 1. Get the data files
Download the following data files from the internet using the curl command: http://eaton-lab.org/pdsb/test.fastq.gz and http://eaton-lab.org/pdsb/iris-data-dirty.csv. Use the less or zless commands to look at the files. Then use the head command to print the first 5 lines from each file as output.
```
curl http://eaton-lab.org/pdsb/test.fastq.gz > test.fastq.gq
gunzip test.fastq.gz
curl http://eaton-lab.org/pdsb/iris-dirty-data.csv > iris-dirty-data.csv
zless test.fastq.gz
zless iris-dirty-data.csv
head -n5 test.fastq.gz
head -n5 iris-dirty-data.csv
```
```
@29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
TGCAGGAAGGAGATTTTCGNACGTAGTGNNNNNNNNNNNNNNGCCNTGGATNNANNNGTGTGCGTGAAGAANAN
+29154_superba.1 GRC13_0027_FC:4:1:12560:1179 length=74
IIIIIIIGIIIIIIFFFFF#EEFE<?################################################
@29154_superba.2 GRC13_0027_FC:4:1:15976:1183 length=74

5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa

```
# II. Clean the data
Use grep, uniq, sed. Check that all of the species names are spelled correctly in the file iris-data-dirty.csv. Also check for missing values stored as NA. Create a new file where mispelled names are replaced with the correct values, and lines with NA are excluded, and save it as iris-data-clean.csv. Use cut, sort and uniq to list the number of data values there are for each species in the new cleaned data file.

```
uniq -u -s 16 iris-data-dirty.csv
```
identify "dirty" data
```
4.8,3.4,1.6,0.2,Iris-setsa
7.0,3.2,4.7,1.4,Iris-versicolour
6.3,NA,4.9,1.5,Iris-versicolor
5.6,NA,4.1,1.3,Iris-versicolor
```
```
sed -i 's/4.8,3.4,1.6,0.2,Iris-setsa/4.8,3.4,1.6,0.2,Iris-setosa/g' iris-data-dirty.csv 
sed -i 's/7.0,3.2,4.7,1.4,Iris-versicolour/7.0,3.2,4.7,1.4,Iris-versicolor/g' iris-data-dirty.csv > iris-data-clean.csv
grep -v NA iris-data-dirty.csv > iris-data-clean.csv
```
modify dirty data in file then write it all to a new clean file
```
cut -c 17- iris-data-clean.csv | sort | uniq -c
```
cut each line for just the species name, then sort and count each unique appearance
```
     50 Iris-setosa
     48 Iris-versicolor
     50 Iris-virginica
```

# III. Summarize sequence data file
Find how many lines in the data file test.fastq.gz start with "TGCAG" and end with "GAG"
```
cut -c 1-5,72-74 test.fastq | grep -c TGCAGGAG
```
cut with a range, then grab (number of) lines that fit TGCAGGAG
the ranges are 1-5 and 72 - 74 for first 5 and last 3 chars


# IV. Summarize sequence data file
Write a for-loop to separate the reads from the file test.fastq.gz based on the taxon name in the label, and write the reads to separately named files in the new directory called sorted_reads/. This will be tricky because each read in the data file spans four lines, so for each read that you correctly identify you must grab the line with the sequence data below it, as well as the repeat label after that, and the quality information line after that. Try to think of what information is in the file that could allow you to fetch the correct reads and pipe them out of this file and into another. Likely tools include mkdir, grep


get unique species names (cut grep uniq)

-AN for grep to grap lines after, where N is number of lines after matching one
need a file for each species name (can make a for loop to touch the reads to the correct file )
```
mkdir sorted_reads/

species=$(grep "^+" test.fastq | cut -d '.' -f 1 | cut -d '_' -f 2 | sort | uniq -d)

for animal in $species; do grep -A 1 "$animal" test.fastq > sorted_reads//$animal.txt; done
```
make new directory but don't change yet since test.fastq is in pdsb, not sorted

functions that define species var allow me to cut around the characters that identify species name (. and _ ), the grep allows me to only grab the lines with species names rather than nt sequences or the other one, sort and uniq just make sure it's organized and no repeats

now have array with species names! so have to loop through these; in loop must grep for the line after each match (since each read (of 4 lines) has 2 lines that include the species name) and touch to matching file in folder

`grep -A 1 cyathophylla test.fastq`
would get the relevant lines; so have to do this in a loop and write it to file by same name 

# Sources

https://www.computerhope.com/unix/uuniq.htm
https://www.computerhope.com/unix/ucut.htm
https://www.computerhope.com/unix/usort.htm
https://www.computerhope.com/unix/ugrep.htm
https://stackoverflow.com/questions/17628867/solve-1-ambiguous-redirect
