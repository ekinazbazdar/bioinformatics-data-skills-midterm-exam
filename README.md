# bioinformatics-data-skills-midterm-exam

# Extracting Multiple Sequences from a FASTA file

Some studies require to remove certain **unwanted sequences** from the **FASTA** file. Following you can find one of the way to extract some unwanted sequences from the **FASTA** file that contain more than **400 sequences**. 

## Aim of the Study

The main aim of this study is to extract hemagglutinin sequences (HA) of influenza A viruses (IAVs) that are originated from poultry, mainly on chickens. This allows to work with the HA sequences that are mainly originated from wild aquatic birds. Accordingly, this study aims to isolate H3 sequences that are obtained from wild aquatic birds circulating in Europe and Asia between 2009-2021.  

## Initial Data

Data used in this study was downloaded from [FluDB-Nucleotide Sequence Search](https://www.fludb.org/brc/influenza_sequence_search_segment_display.spg?method=ShowCleanSearch&decorator=influenza) as a .tar **(iav-h3-sequences.tar)** file with the following selection criteria: 

Data Type: Genome Segments,
Virus Type: A,
Subtype: H3,
Date Range: 2009-2021,
Select Segments: HA (Complete),
Host: Avian,
Geographic Grouping: Asia and Europe

## Extracting tar file

The tar file that contains fasta sequences was extracted using followed command: 

```bash
% tar -xvf iav-h3-sequences.tar 
```

The name of the extracted file: **455015590871-H3.fasta** 

## Determination of Unwanted Sequences

The number of chicken origin H3 sequences was determined using the search of *Host:Chicken* in the main fasta file and these headers were saved in a new file which called as **headers.txt**.

```bash
% grep -i "Host:Chicken" 455015590871-H3.fasta > headers.txt
% grep -c "Host:Chicken" headers.txt
```

The sequences that were originated from chickens were determined as **33**.

## Creating Linear Lines

The number of lines that contain sequence header were determined as **455** sequences based on the feature which first letter of the header is '>' using followed command:

```bash
% grep -ic ">" 455015590871-H3.fasta
```

Each line starting with ">" was formed in a linear format in a new fasta file called as **linear.fasta** using followed command:

```bash
% awk '/^>/ {printf("%s%s\t",(N>0?"\n":""),$0);N++;next;} {printf("%s",$0);} END {printf("\n");}' < 455015590871-H3.fasta > linear.fasta
```

Consequently, each header merged with corresponding sequence. This allowed to delete intended headers which include sequences originating from chickens along with the intended sequences.

## Sequence Deletion

Intended sequences were removed using followed command:

```bash
% sed -e '/Host:Chicken/d' linear.fasta > rm-linear.fasta
```

Sequence removed version of the file was named as **rm-linear.fasta**.

To whether check the deletion was successful, total number of sequences and the number of *Host:Chicken* related sequences were determined using followed commands:

```bash
% grep -ic ">" rm-linear.fasta 
% grep -ic "Host:Chicken" rm-linear.fasta
```

As a result, total number of sequences and the number of *Host:Chicken* related sequences were determined as **412** and **0**, respectively. This indicated that all sequences belonging to chickens were removed and total number of sequences decreased.

The differences between **linear.fasta** and **rm.linear.fasta** files were also investigated and saved as a separate file which called as **differences.txt**:

```bash
% diff -u linear.fasta rm-linear.fasta > differences.txt
```

## Re-linearization

As a final step, re-linear form of the file was restored in a new file which called as **final.fasta** using followed command:

```bash
% tr "\t" "\n" < rm-linear.fasta > final.fasta
```
