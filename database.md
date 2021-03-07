```bash
%%bash
cat pEcoli_BioSample.txt | wc -l
cat pSalmonella_Biosample.txt | wc -l
cat pShigella_Biosample.txt | wc -l
cat pYersinia_Biosample.txt| wc -l
```

        1506
        1022
          48
          58


### Get genome data base

NCBI > Genome > search > Microbes > Browse microbial genomes >> complete (filter)


```python
ls *csv*
```

    Ecoli.csv              Salmonella.csv         Yersinia.csv
    Nsalmonella.csv        Shigella flexneri.csv


### Cut biosample from csv files
1. Escherichai coli
2. Shigalla flexneri
3. Salmonella enterica
4. Yersinia pestis


```bash
%%bash
cat Ecoli.csv | grep coli | cut -d '"' -f8 > 1_biosample.txt
cat Shigella\ flexneri.csv | grep flexneri | cut -d '"' -f8  >2_biosample.txt
cat Salmonella.csv | grep enterica | cut -d '"' -f8  > 3_biosample.txt
cat Yersinia.csv | grep pestis | cut -d '"' -f8 > 4_biosample.txt
```


```python
ls *_biosample.txt
```

    1_biosample.txt  2_biosample.txt  3_biosample.txt  4_biosample.txt


### Get SRS and biosample from biosampleID


```bash
%%bash

epost -db biosample -input 1_biosample.txt -format acc|\
efetch -format xml |\
xtract -pattern BioSample -SRA "(-)" -block Id -if Id@db -equals "SRA" -SRA Id -block Ids -first Id -element "&SRA" \
> 1.biosample.SRS.txt

epost -db biosample -input 2_biosample.txt  -format acc|\
efetch -format xml |\
xtract -pattern BioSample -SRA "(-)" -block Id -if Id@db -equals "SRA" -SRA Id -block Ids -first Id -element "&SRA" \
> 2.biosample.SRS.txt

epost -db biosample -input 3_biosample.txt -format acc|\
efetch -format xml |\
xtract -pattern BioSample -SRA "(-)" -block Id -if Id@db -equals "SRA" -SRA Id -block Ids -first Id -element "&SRA" \
> 3.biosample.SRS.txt

epost -db biosample -input 4_biosample.txt -format acc|\
efetch -format xml |\
xtract -pattern BioSample -SRA "(-)" -block Id -if Id@db -equals "SRA" -SRA Id -block Ids -first Id -element "&SRA" \
> 4.biosample.SRS.txt
```

#### check content with biosample 


```bash
%%bash
cat 1_biosample.txt | wc -l
cat 1_biosample.txt | sort | uniq | wc -l
cat 1.biosample.SRS.txt | wc -l

cat 2_biosample.txt | wc -l
cat 2_biosample.txt | sort | uniq | wc -l
cat 2.biosample.SRS.txt | wc -l

cat 3_biosample.txt | wc -l
cat 3_biosample.txt | sort | uniq | wc -l
cat 3.biosample.SRS.txt | wc -l

cat 4_biosample.txt | wc -l
cat 4_biosample.txt |sort | uniq | wc -l
cat 4.biosample.SRS.txt | wc -l
```

        1506
        1505
        1505
          48
          48
          48
        1022
        1016
        1016
          58
          58
          58


    1.Ecoli biosample :      2 SAMN13951916
    
    3.Salmonella biosample:  2 SAMEA5976334
                             2 SAMN00991042
                             2 SAMN00991044
                             2 SAMN00991047
                             2 SAMN01088008
                             2 SAMN01823701

### Get SRSID from files


```bash
%%bash
cat 1.biosample.SRS.txt | cut -f2 > 1_srsID.txt
cat 2.biosample.SRS.txt | cut -f2 > 2_srsID.txt
cat 3.biosample.SRS.txt | cut -f2 > 3_srsID.txt
cat 4.biosample.SRS.txt | cut -f2> 4_srsID.txt
```

### Get SRS SRR and Sequencer from SRSID

1.Ecoli


```bash
%%bash
epost -db sra -input 1_srsID.txt -format acc |\
esummary -format runinfo -mode xml |\
xtract -pattern Row -element Sample acc,Run,Platform instrument_model\
> 1.SRS.SRR.seqmeth.txt

epost -db sra -input 2_srsID.txt -format acc |\
esummary -format runinfo -mode xml |\
xtract -pattern Row -element Sample acc,Run,Platform instrument_model\
> 2.SRS.SRR.seqmeth.txt

epost -db sra -input 3_srsID.txt -format acc |\
esummary -format runinfo -mode xml |\
xtract -pattern Row -element Sample acc,Run,Platform instrument_model\
> 3.SRS.SRR.seqmeth.txt

epost -db sra -input 4_srsID.txt -format acc |\
esummary -format runinfo -mode xml |\
xtract -pattern Row -element Sample acc,Run,Platform instrument_model\
> 4.SRS.SRR.seqmeth.txt

```

#### Check content with SRSID 


```bash
%%bash 
cat 1_srsID.txt | sort | uniq | wc -l
cat 1.SRS.SRR.seqmeth.txt | cut -f 1 | sort | uniq | wc -l

cat 2_srsID.txt | sort | uniq | wc -l
cat 2.SRS.SRR.seqmeth.txt | cut -f 1 | sort | uniq | wc -l

cat 3_srsID.txt | sort | uniq | wc -l
cat 3.SRS.SRR.seqmeth.txt | cut -f 1 | sort | uniq | wc -l

cat 4_srsID.txt | sort | uniq | wc -l
cat 4.SRS.SRR.seqmeth.txt | cut -f 1 | sort | uniq | wc -l

```

         625
         568
          15
          12
         634
         614
          25
          22


#### Find missing content


```bash
%%bash
cat 1.SRS.SRR.seqmeth.txt | cut -f 1 > /tmp/checkSRS1.txt
cat 2.SRS.SRR.seqmeth.txt | cut -f 1 > /tmp/checkSRS2.txt
cat 3.SRS.SRR.seqmeth.txt | cut -f 1 > /tmp/checkSRS3.txt
cat 4.SRS.SRR.seqmeth.txt | cut -f 1 > /tmp/checkSRS4.txt
```


```python
f1 = open('/tmp/checkSRS1.txt')
f2 = open('1_srsID.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2

difSRS = "".join(dif)
#print(difSRS)

file = open("/tmp/difSRS1.txt", "w")
n = file.write(difSRS)
text_file.close()

```


```bash
%%bash
epost -db sra -input /tmp/difSRS1.txt -format acc |head
```

    ERROR in count output: Empty result - nothing to do
    URL: https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=sra&query_key=1&WebEnv=MCID_60433694b00294426c04c0df&retmax=0&usehistory=y&edirect=13.9&edirect_os=darwin&tool=entrez-direct-count&email=rapeephanduangjanchot@rapeephans-MacBook-Air.local
    



```python
f1 = open('/tmp/checkSRS2.txt')
f2 = open('2_srsID.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2

difSRS = "".join(dif)
print(difSRS)

file = open("/tmp/difSRS2.txt", "w")
n = file.write(difSRS)
text_file.close()

```

    ERS4821025
    ERS4821024
    -
    



```bash
%%bash
epost -db sra -input /tmp/difSRS2.txt -format acc |head
```

    ERROR in count output: Empty result - nothing to do
    URL: https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=sra&query_key=1&WebEnv=MCID_6043375ec76a705e791cc480&retmax=0&usehistory=y&edirect=13.9&edirect_os=darwin&tool=entrez-direct-count&email=rapeephanduangjanchot@rapeephans-MacBook-Air.local
    



```python
f1 = open('/tmp/checkSRS3.txt')
f2 = open('3_srsID.txt')

list1 = f1.readlines()
list2 = f2.readlines()

set1 = set(list1)
set2 = set(list2)

dif = a^b
difSRS = "".join(dif)
print(dif)

file = open("/tmp/difSRS3.txt", "w")
n = file.write(difSRS)
text_file.close()

```

    set()



```bash
%%bash
epost -db sra -input /tmp/difSRS3.txt -format acc |head
```

    Failure of post to find data to load



```bash
%%bash
cat /tmp/checkSRS3.txt | sort | uniq | wc -l
cat 3_srsID.txt | sort | uniq | wc -l
```

         614
         634



```python
f1 = open('/tmp/checkSRS4.txt')
f2 = open('4_srsID.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2

difSRS = "".join(dif)
print(difSRS)

file = open("/tmp/difSRS4.txt", "w")
n = file.write(difSRS)
text_file.close()
```

    -
    ERS000059
    SRS751214
    



```bash
%%bash
epost -db sra -input /tmp/difSRS4.txt -format acc |head
```

    ERROR in count output: Empty result - nothing to do
    URL: https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=sra&query_key=1&WebEnv=MCID_60433a066fc57325ca1d6048&retmax=0&usehistory=y&edirect=13.9&edirect_os=darwin&tool=entrez-direct-count&email=rapeephanduangjanchot@rapeephans-MacBook-Air.local
    


### Get nucleotide info from biosampleID


```bash
%%bash
epost -db biosample -input 1_biosample.txt -format acc |\
elink -target nuccore | efetch -format docsum \
>1_Nucinfo.docsum

```


```bash
%%bash
epost -db biosample -input 2_biosample.txt -format acc |\
elink -target nuccore | efetch -format docsum \
>2_Nucinfo.docsum
```


```bash
%%bash
epost -db biosample -input 3_biosample.txt -format acc |\
elink -target nuccore | efetch -format docsum \
>3_Nucinfo.docsum
```


```bash
%%bash
epost -db biosample -input 4_biosample.txt -format acc |\
elink -target nuccore | efetch -format docsum \
>4_Nucinfo.docsum
```

### Extact biosample, nucleotideID and genome type from files


```bash
%%bash
cat 1_Nucinfo.docsum |\
xtract -pattern DocumentSummary -element BioSample Caption, Genome \
>1.biosample.nucID.genome1.txt

cat 2_Nucinfo.docsum |\
xtract -pattern DocumentSummary -element BioSample Caption, Genome \
>2.biosample.nucID.genome.txt

cat 3_Nucinfo.docsum |\
xtract -pattern DocumentSummary -element BioSample Caption, Genome \
>3.biosample.nucID.genome1.txt

cat 4_Nucinfo.docsum |\
xtract -pattern DocumentSummary -element BioSample Caption, Genome \
>4.biosample.nucID.genome.txt
```

#### check content with biosample


```bash
%%bash
cat 1_biosample.txt | sort | uniq | wc -l
cat 1.biosample.nucID.genome1.txt | cut -f1 | sort | uniq | wc -l

cat 2_biosample.txt | sort | uniq | wc -l
cat 2.biosample.nucID.genome.txt | cut -f1 | sort | uniq | wc -l

cat 3_biosample.txt | sort | uniq | wc -l
cat 3.biosample.nucID.genome1.txt | cut -f1 | sort | uniq | wc -l

cat 4_biosample.txt | sort | uniq | wc -l
cat 4.biosample.nucID.genome.txt | cut -f1 | sort | uniq | wc -l
```

        1505
        1000
          48
          48
        1016
        1000
          58
          58


#### Find missing content

1.Ecoli


```python
cat 1.biosample.nucID.genome1.txt | cut -f1 > /tmp/checkBS1.txt
```


```python
f1 = open('/tmp/checkBS1.txt')
f2 = open('1_biosample.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2

difBS = "".join(dif)
#print(difBS)

file = open("/tmp/difBS1.txt", "w")
n = file.write(difBS)
file.close()
```


```bash
%%bash
epost -db biosample -input /tmp/difBS1.txt -format acc |\
elink -target nuccore | efetch -format docsum > 1_Nucinfo2.docsum
```


```bash
%%bash
cat 1_Nucinfo2.docsum |\
xtract -pattern DocumentSummary -element BioSample Caption, Genome \
>1.biosample.nucID.genome2.txt
```


```bash
%%bash
cat 1.biosample.nucID.genome2.txt | cut -f1 | sort | uniq | wc -l
cat 1.biosample.nucID.genome2.txt | cut -f1 | sort | uniq > /tmp/checkBS12.txt
```

         505



```python
f1 = open('/tmp/checkBS1.txt')
f2 = open('1_biosample.txt')
f3 = open('/tmp/checkBS12.txt')

set1 = set(f1.readlines()) | set(f3.readlines())
set2 = set(f2.readlines())

dif = set1^set2
print(dif)
```

    set()



```bash
%%bash
cat 1_biosample.txt | sort | uniq | wc -l
cat 1.biosample.nucID.genome1.txt | cut -f1| sort | uniq | wc -l
cat 1.biosample.nucID.genome2.txt | cut -f1| sort | uniq | wc -l
```

        1505
        1000
         505


2.Salmonella


```python
cat 3.biosample.nucID.genome1.txt | cut -f1 | tr ',' '\n' > /tmp/checkBS3.txt
```


```bash
%%bash
cat 3_biosample.txt | sort | uniq | wc -l
cat /tmp/checkBS3.txt | sort | uniq | wc -l
```

        1016
        1001



```python
f1 = open('/tmp/checkBS3.txt')
f2 = open('3_biosample.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2

difBS = "".join(dif)

#print(difBS)

file = open("/tmp/difBS3.txt", "w")
n = file.write(difBS)
text_file.close()
```


```bash
%%bash
epost -db biosample -input /tmp/difBS3.txt -format acc |\
elink -target nuccore | efetch -format docsum > 3_Nucinfo2.docsum
```


```bash
%%bash
cat 3_Nucinfo2.docsum |\
xtract -pattern DocumentSummary -element BioSample Caption, Genome \
>3.biosample.nucID.genome2.txt
```


```bash
%%bash
cat 3_biosample.txt | sort | uniq | wc -l
cat /tmp/checkBS3.txt | sort | uniq | wc -l
cat 3.biosample.nucID.genome2.txt | cut -f1 | sort | uniq | wc -l
```

        1016
        1001
          17


#### Combine files


```bash
%%bash
cat 1_biosample.txt | sort | uniq | wc -l
cat 1.biosample.nucID.genome1.txt 1.biosample.nucID.genome2.txt |cut -f1 | sort | uniq | wc -l

cat 3_biosample.txt | sort | uniq | wc -l
cat 3.biosample.nucID.genome1.txt 3.biosample.nucID.genome2.txt |cut -f1 | sort | uniq | wc -l

cat 1.biosample.nucID.genome1.txt 1.biosample.nucID.genome2.txt | sort | uniq \
> 1.biosample.nucID.genome3.txt
cat 3.biosample.nucID.genome1.txt 3.biosample.nucID.genome2.txt | sort | uniq \
> 3.biosample.nucID.genome3.txt

```

        1505
        1505
        1016
        1016


### Get AssemblyID and BiosampleID from BiosampleID


```bash
%%bash
epost -db biosample -input 1_biosample.txt -format acc |\
elink -target assembly  |\
efetch -format docsum > 1_assaccinfo.docsum

```


```bash
%%bash
epost -db biosample -input 2_biosample.txt -format acc |\
elink -target assembly  |\
efetch -format docsum > 2_assaccinfo.docsum


```


```bash
%%bash
epost -db biosample -input 3_biosample.txt -format acc |\
elink -target assembly  |\
efetch -format docsum > 3_assaccinfo.docsum


```


```bash
%%bash
epost -db biosample -input 4_biosample.txt -format acc |\
elink -target assembly  |\
efetch -format docsum > 4_assaccinfo.docsum

```


```bash
%%bash
cat 1_assaccinfo.docsum |\
xtract -pattern DocumentSummary -element BioSampleAccn, AssemblyAccession \
> 1.biosample.assacc1.txt

cat 2_assaccinfo.docsum |\
xtract -pattern DocumentSummary -element BioSampleAccn, AssemblyAccession \
> 2.biosample.assacc.txt

cat 3_assaccinfo.docsum |\
xtract -pattern DocumentSummary -element BioSampleAccn, AssemblyAccession \
> 3.biosample.assacc.txt

cat 4_assaccinfo.docsum |\
xtract -pattern DocumentSummary -element BioSampleAccn, AssemblyAccession \
> 4.biosample.assacc.txt


```

Check content from biosampleID


```bash
%%bash

cat 1_biosample.txt | sort | uniq | wc -l
cat 1.biosample.assacc1.txt | cut -f1| sort | uniq | wc -l

cat 2_biosample.txt | sort | uniq | wc -l
cat 2.biosample.assacc.txt | cut -f1 | sort | uniq | wc -l

cat 3_biosample.txt | sort | uniq | wc -l
cat 3.biosample.assacc.txt | cut -f1| sort | uniq | wc -l

cat 4_biosample.txt | sort | uniq | wc -l
cat 4.biosample.assacc.txt | cut -f1| sort | uniq | wc -l


```

        1505
        1000
          48
          48
        1016
        1000
          58
          58


1.Ecoli


```python
cat 1.biosample.assacc1.txt | cut -f1 > /tmp/checkass1.txt
```


```python
f1 = open('/tmp/checkass1.txt')
f2 = open('1_biosample.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2
#print(dif)

difAss = "".join(dif)


file = open("/tmp/difAss1.txt", "w")
n = file.write(difAss)
file.close()

```


```bash
%%bash
epost -db biosample -input /tmp/difAss1.txt -format acc |\
elink -target assembly  |\
efetch -format docsum > 1_assaccinfo2.docsum
```


```bash
%%bash
cat 1_assaccinfo2.docsum |\
xtract -pattern DocumentSummary -element BioSampleAccn, AssemblyAccession \
> 1.biosample.assacc2.txt
```


```bash
%%bash

cat 1_biosample.txt | sort | uniq | wc -l
cat 1.biosample.assacc1.txt | cut -f1| sort | uniq | wc -l
cat 1.biosample.assacc2.txt |  cut -f1| sort | uniq | wc -l
```

        1505
        1000
         505


3.Salmonella


```python
cat 3.biosample.assacc.txt | cut -f1 > /tmp/checkass3.txt
```


```python
f1 = open('/tmp/checkass3.txt')
f2 = open('3_biosample.txt')

set1 = set(f1.readlines())
set2 = set(f2.readlines())

dif = set1^set2
#print(dif)

difAss = "".join(dif)

file = open("/tmp/difAss3.txt", "w")
n = file.write(difAss)
file.close()


```


```bash
%%bash
epost -db biosample -input /tmp/difAss3.txt -format acc |\
elink -target assembly  |\
efetch -format docsum > 3_assaccinfo2.docsum
```


```bash
%%bash
cat 3_assaccinfo2.docsum |\
xtract -pattern DocumentSummary -element BioSampleAccn, AssemblyAccession \
> 3.biosample.assacc2.txt
```


```bash
%%bash

cat 3_biosample.txt | sort | uniq | wc -l
cat 3.biosample.assacc.txt | cut -f1| sort | uniq | wc -l
cat 3.biosample.assacc2.txt |  cut -f1| sort | uniq | wc -l
```

        1016
        1000
          16


Conbine files


```bash
%%bash

cat 1.biosample.assacc1.txt 1.biosample.assacc2.txt > 1.biosample.assacc3.txt
cat 3.biosample.assacc1.txt 3.biosample.assacc2.txt > 3.biosample.assacc3.txt
```


```python
cat 1.biosample.assacc3.txt | wc -l
```

        1513



```python

```
