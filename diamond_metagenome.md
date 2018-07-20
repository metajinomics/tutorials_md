# Diamond metagenome tutorial
This tutorial shows how to annotate metagenome using DIAMOND

### Trim your sequencing file
```
for x in *fastq.gz;
do java -jar ../Trimmomatic-0.38/trimmomatic-0.38.jar PE $x ${x%R1*}R2.fastq.gz ${x%_1*}.r1.pe ${x%_1*}.r1.se ${x%_1*}.r2.pe ${x%_1*}.r2.se ILLUMINACLIP:../Trimmomatic-0.38/adapters/TruSeq3-PE-2.fa:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36;
done 
```

### prepare Database
```
diamond makedb --in genus_prokaryotes.pep -d kegg.prokaryotes
```

### Run blastX using DIAMOND
```
for x in *.pe;
do diamond blastx -d kegg.prokaryotes -q $x -o $x.m8;
done
```

### Find the best hit
```
git clone https://github.com/metajinomics/dev.git
for x in *.m8;
do python dev/blast_tools/best_hit.py $x > $x.besthit;
done
```

### (Option) find sequences that both R1 and R2 hit
```
for x in *R1*m8.besthit;
do python dev/blast_tools/both_hit.py $x ${x%R1.pe*}R2.pe.m8.besthit > ${x%_R1.pe*}.bothhit;
done
```

### make count table
```
python dev/blast_tools/make_table.py genus_prokaryotes.pep *.bothhit > kegg.bothhit.counttable.tsv
```
