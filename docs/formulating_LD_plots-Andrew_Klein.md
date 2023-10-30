# Making a Pairwise LD Plot Using LDBlock Show
__Andrew Klein__  andrewjklein@arizona.edu
Our goal is to create a pairwise LD plot of the SNPs used to determine ABO blood type alleles. These SNPs were identified during a systematic review and include both functional SNPs and tag SNPs. 

The O vs non-O SNPs include:
* rs8176719
* rs8176704
* rs687289
* rs657152
* rs612169
* rs8176645
* rs505922
* rs529565

The A vs B SNPs include: 
* rs8176749
* rs8176747
* rs8176746
* rs8176743
* rs8176741
* rs7853989
* rs8176722
* rs8176720
* rs8176672

The documented code will be run on the Karnes Server and we are making the LD plots using the pangenomic 1000g file that can be found [here](https://www.internationalgenome.org/data-portal/data-collection/30x-grch38).
This file will also contain the notes/thoughts that Andrew was thinking about while making these plots. You will see how he learned how to use PLINK, command line, and LDBlockShow as this file goes on

# Files Being Used
All of these files can be found on the Karnes Server under __/Data/home/ajklein__ or __/Data/home/ajklein/O_Files__ or __/Data/home/ajklein/B_Files__
Original Pangenomic 1000Genome file is called __1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz__<br>
The final VCF files that are being used are:<br>
__o_snps_chr9_1kgp_high_coverage_recoded.vcf.gz__ <br>
__O_AMR.vcf__  __B_AMR.vcf__<br>
__O_AFR.vcf__  __B_AFR.vcf__<br>
__O_EUR.vcf__  __O_EUR.vcf__<br>
__O_EAS.vcf__  __O_EAS.vcf__<br>
__O_SAS.vcf__  __O_SAS.vcf__<br>

CSV file that contains all the data from the 1000 genomes individuals<br>
__1000G_2504_high_coverage.sequence.index.csv__ <br>

Later in this document, I will explain how these files are made

# Programs Being Used

**LDBlockShow**(https://github.com/BGI-shenzhen/LDBlockShow) is a fast and effective tool to generate linkage disequilibrium (LD) heatmap from VCF files. Here is the [LDBlockShow GitHub](https://github.com/BGI-shenzhen/LDBlockShow) and the [LDBlockShow article](https://doi.org/10.1093/bib/bbaa227). [LDBLockShow Manual](https://github.com/hewm2008/LDBlockShow/blob/main/LDBlockShow_Manual_English.pdf)<br>


To call upon this program in the Karnes server use this command:<br>
__/home/shared/tools/LDBlockShow-1.40/bin/LDBlockShow__<br>
Now here are some other commands I will be using:<br>
* `-InVCF`: the VCF input
* `-OutPut`: the output file of LD blocks
* `-Region`: the region to show the LD info in
* `-OutPdf`: specifying to output as a PDF
* `ShowNum`: shows the R2 values in each cell
* `-SeleVar 2`: `1` specifies D prime calculations and `2` specifies r^2 calculation
* `-SpeSNPName`: used to indicate the names for interested SNPs, these names will be shown in the heatmap.
* `-MAF`: Minor allele frequency filter
* `-InGFF`: the GFF3 input for genomic region annotation


**ShowLDSVG**(https://github.com/BGI-shenzhen/LDBlockShow) This is a program included with LDBlockShow. This program is used to change the aesthetics of the plots that LDBlockShow generates. As of 4/6/2023 Andrew has attemtped to use this program but hasn't figured out everything yet. When he begins to tweak the plots this section may be updated. <br>
To call upon this program in the Karnes server use this command:<br>
__/home/shared/tools/LDBlockShow-1.40/bin/ShowLDSVG__<br>
* `-InPreFix`: The output of LD Block Show (it will grab the correct file type you just need to put in the correct directory)
* `-OutPut`: Used to name the output file and choose where it goes
* `-OutPdf`: specifying to output as a PDF
* `ShowNum`: shows the R2 values in each cell



**PLINK 1.90 beta** (https://www.cog-genomics.org/plink/) Plink is a program that allows users to view, edit, and organize multiple types of files. Developed by Christopher Chang with support from the NIH-NIDDK's Laboratory of Biological Modeling, the Purcell Lab, and others. 

To call upon this program in the Karnes server use this command:<br>
__plink1.9__<br>
Here are some arguments I will be using:<br"
* `--vcf`: Inputs the VCF file
* `--extract`: Extracts SNPs based on a txt file that tells PLINK the positions of the SNPs
* `--recode vcf`: changes the output to a vcf file
* `--out`: tells the program where to put the output files
* `--keep`: Accepts a txt file that contains the SAMPLE_ID's if individuals and removes everyone else
* `--make-bed`: I don't know why I need this argument but the program tells you to add it in and it didn't work without it
* `--double-id`: Allows PLINK to ignore any _ in the txt file (Shout out to Christopher Chang for this tip)

__3/14/2023__    __Andrew's First Attempt__

This was the first time Andrew attempted to use LDBlock Show. This is the code he used:

<span style="color:darkgreen"> Takes a vcf as input, look at a certain region, produces a PNG LD Plot </span>
<pre> /home/shared/tools/LDBlockShow-1.40/bin/LDBlockShow -InVCF /Data/home/ajklein/1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz -OutPut /Data/home/ajklein/LDBlock -Region chr9:133247534-133278152 -OutPng -ShowNum -SeleVar 2 </pre>

He didn't know how to output the graph as a PDF yet but this first attempt showed every SNP in the region that he provied in the code. While this was not even close to what he wanted to do he was able to use the program.\
He did some more research and added some more arguments to his code:

<span style="color:darkgreen"> Take a vcf and a txt files as input, looks at a certain region, label the snps from the txt document, outputs a LD Plot as a PDF </span>
<pre>/home/shared/tools/LDBlockShow-1.40/bin/LDBlockShow -InVCF /Data/home/ajklein/1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz -OutPut /Data/home/ajklein/LDBlock_FULL -Region chr9:133247534-133278152 -OutPdf -ShowNum -SeleVar 2 -SpeSNPName /Data/home/ajklein/O_Snps_Positions.txt -InGFF /Data/home/ajklein/ABOGene.GFF3</pre>

Now he was able to output the graphs as a PDF, label the SNPs he was interested in and tried to use a GFF3 file to show exons and label the gene name. But the -InGFF argument was not working for him at this time.

__3/15/2023 Andrew Tries Again__

At this point, Andrew was trying to think of a way to only calculate the SNPs he was interested in. He had ideas about how to do this but he went straight to the source by asking the creator of the program on GitHub. The creator seemed to be active with ~3 day response time. While waiting for a response he continued to learn command line. He learned how to use the "grep" and "zcat" command to find what SNPs are in a VCF file. He was then introduced to PLINK. He will use PLINK to extract the B and O SNPs that he cares about. 


First, he used zcat and the grep command to look through the vcf file to figure out how the SNPs he cared about are named. He used genome browser to obtain the position of each SNP and used this position to look for each SNP in the VCF file.
<pre>zcat 1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz | grep "133274084"</pre>


He repeated this command for each SNP and this revealed that his SNPs were documented as __"9:133266790:C:A"__. He had to copy these locations into a txt file so he can use PLINK to extract these specific SNPs
Now that he had all the positions of the SNPs he had to format the txt file in a specific way. One line of this txt file looked like this:

__chr9	133256205	133256205	9:133256205:G:C__

This will tell PLINK what SNPs to extract. He was then able to run this code:

<span style="color:darkgreen">
Creates a new VCF file that only contains the O SNPs
    </span>
<pre>plink1.9 --vcf /Data/home/ajklein/1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz --extract O_Snps_Positions.txt --recode vcf --out O_Snps_chr9_1kgp_high_coverage</pre>

__3/20/2023 The First Roadblock__

Now that Andrew had the filtered VCF file he tried running LDBlock and at first it looked like it created an LD plot of just the O snps but after a careful look the SNPs were not labeled and there was 7 SNPs not 9. He asked the creator of the program on GitHub why the labels were not showing up. It turns out when the filtered VCF was created chromosome 9 was no longer labeled as chr9, it was now labeled 9. Using nano to adjust the txt file for LDBlock show the labels began to show up. The next issue was getting all of the SNPs to show. The creator introduced Andrew to the -MAF command.

<span style="color:darkgreen">
    Take a vcf and a txt files as input, looks at a certain region, labels the snps from the txt document, reduces the MAF to 0.0001, outputs a LD Plot as a PDF
    </span>
<pre>/home/shared/tools/LDBlockShow-1.40/bin/LDBlockShow -InVCF /Data/home/ajklein/O_Snps_chr9_1kgp_high_coverage.vcf.gz -OutPut /Data/home/ajklein/LDBlock_V3 -Region 9:133247534-133278152 -OutPdf -ShowNum -SeleVar 2 -SpeSNPName /Data/home/ajklein/O_Snps_Positions.txt -InGFF /Data/home/ajklein/ABOGene.GFF3</pre>


After adding this command Andrew was able to get one of the missing SNPs to show.

After looking at the log from LDBlock show Andrew realized the reason the last SNP wasn't showing up. This last SNP was an indel, indels notoriously have issues in these types of programs. He explained the issue to Kiana who was able to brute force recode this indel. (She created a markdown file of how she did this.) Once Kiana was able to do this, Andrew could continue his journey.

__3/23/2023 The B SNPs__

In the meantime, he began to do the same thing for the B SNPs.

<pre>plink1.9 --vcf /Data/home/ajklein/1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz --extract B_Snps_Positions_Plink.txt --recode vcf --out B_Snps_chr9_1kgp_high_coverage</pre>
<pre>/home/shared/tools/LDBlockShow-1.40/bin/LDBlockShow -InVCF /Data/home/ajklein/1kGP_high_coverage_Illumina.chr9.filtered.SNV_INDEL_SV_phased_panel.vcf.gz -OutPut /Data/home/ajklein/B_LDBlock_1 -Region chr9:133247534-133278152 -OutPdf -ShowNum -SeleVar 2 -SpeSNPName /Data/home/ajklein/B_Snps_Positions.txt -InGFF /Data/home/ajklein/ABOGene.GFF3</pre>

He did the same thing for B as he did for O. Luckily none of the B SNPs were indels so he did not have any issues with this set of SNPs.

__4/3/23 Return to the O SNPs__

While waiting for Kiana to figure out the indel Andrew took a week to work on school and try scientific writing for the first time. As referenced before Kiana was able to fix the indel issue. After Andrew received the new recoded vcf file we was able to fix the graph.

<span style="color:darkgreen">
    Take a vcf and a txt files as input, looks at a certain region, labels the snps from the txt document, reduces MAF to 0.001, looks at the GFF file for exon and intron information, outputs a LD Plot as a PDF
    </span>
<pre>/home/shared/tools/LDBlockShow-1.40/bin/LDBlockShow -InVCF /Data/home/ajklein/O_Files/o_snps_chr9_1kgp_high_coverage_recoded.vcf.gz -OutPut /Data/home/ajklein/O_Files/O_LDBlock -Region 9:133247534-133278152 -OutPdf -ShowNum -SeleVar 2 -SpeSNPName /Data/home/ajklein/B_Snps_Positions.txt -MAF 0.0001 -InGFF /Data/home/ajklein/ABOGene.GFF</pre>


Andrew looked into the -InGFF argument and changed the ABOGene file to a GFF file and changing the file extention showed the exons in the resulting graph. Andrew also made two folders where he would separate the O and B files.

After completing two LD Block graphs for the O and B SNPs he was told to make more of these plots but for the 5 Populations (AMR, AFR, EUR, EAS, and SAS).

Before we move on there is another program that comes with LDBlock Show and this program is __ShowLDSVF__ which Andrew will use in the future to tweak the aesthetics of the LD Plots.

__4/5/2023 Population Specifc LD Plots__

In order to make population specific LD Plots here's what Andrew needed to do:<br>
* 1 Extract population Sample IDs from a vcf file
* 2 Create a txt file for each population that Pink will use to extract individuals
* 3 Use Plink to make 10 new vcf files (5 populations, 2 blood types)
* 4 Use these new vcf files in LDBlockShow to generate 10 new LD Plots<br>

Kiana gave Andrew a large csv file containing the ID data from the individuals from the 1000 genome data.<br>
__1000G_2504_high_coverage.sequence.index.csv__

For some reason in the original 1000 genomes vcf file the way the individuals are identified looks like this:<br>
__HG03063_HG03063__

And in order to get plink to extract these individuals from the vcf file the --keep argument in Plink needs to look like this:<br>
__HG03063_HG03063__ &emsp; __HG03063_HG03063__ <br>

Here is the code that Andrew wrote to categorize the sub populations into the continental populations: (he originally named this csv as "gamers")


<span style="color:darkgreen">
    This creates a new column named "Continent" and sorts individuals into their continental population
    </span>
<pre>list =["GIH", "PJL", "BEB", "STU", "ITU", "SAS"] 
for i in list:
    gamers.loc[gamers['POPULATION'] == i, "Continent"] = "SAS"
list = ["CEU","TSI", "FIN", "GBR", "IBS", "EUR"]
for i in list:
    gamers.loc[gamers['POPULATION'] == i, "Continent"] ="EUR"
list = ["CHB", "JPT", "CHS", "CDX","KHV", "EAS"]
for i in list:
    gamers.loc[gamers['POPULATION'] == i, "Continent"] ="EAS"
list = ["MXL", "PUR", "CLM","PEL", "AMR"]
for i in list:
    gamers.loc[gamers['POPULATION'] == i, "Continent"] ="AMR"
list = ["YRI", 'LWK', "GWD", "MSL", "ESN", "ASW", 'ACB', "AFR"]
for i in list:
    gamers.loc[gamers['POPULATION'] == i, "Continent"] ="AFR"</pre>



<span style="color:darkgreen">
    Sorts the csv file via the "Continent" column
    </span>
<pre>csv.arrange= gamers.sort_values(by="Continent", ascending=True)
csv.arrange.reset_index(drop=True,inplace=True)
csv.arrange</pre>



<span style="color:darkgreen">
Creates a new column called "FAMILY_ID" which is in the format that plink needs.
</span>
<pre>csv.arrange["FAMILY_ID"] = csv.arrange["SAMPLE_NAME"] + '_'+csv.arrange["SAMPLE_NAME"]
csv.arrange</pre>


Andrew didn't know how to delete all the columns that he didn't need so he just deleted them from the csv file manually only leaving the columns he needed. (SAMPLE_NAME, Population, Continent, and FAMILY_ID).<br>

Now he wrote some code to create a txt file that he can use in plink

<span style="color:darkgreen">
This will create a new txt file, look through the "Continent" column looking for "AMR" and when ever there is "AMR" write the Family_ID tabspace Family_ID newline into the txt file.</span>
<pre>AMR = open("amr.txt", "w") 
for i in range(len(csv)):
    row= csv.iloc[i]
    if row["Continent"] == "AMR":
        print(row["FAMILY_ID"])
        AMR.write(row["FAMILY_ID"] + "\t" +row['FAMILY_ID']+ "\n")
AMR.close()</pre>

Andrew did this for the 5 populations. Resulting in 5 txt files.<br>
Now he could use them in Plink.


<span style="color:darkgreen">
The input is a vcf file and a txt file. This will only keep the individuals that are in the txt file. It will then output a new vcf that only contains the individuals of interest.</span>
<pre> plink1.9 --vcf /Data/home/ajklein/B_Files/B_Snps_chr9_1kgp_high_coverage.vcf --keep /Data/home/ajklein/amr.txt --out B_AMR --make-bed --double-id --recode vcf</pre>

This is an example of how to make one of the population LD plots reflect the color of the population we're choosing for our paper:

<span style="color:darkgreen">
This will result in a LD plot that has a color gradient that reflects the color of the population</span>
<pre>/home/shared/tools/LDBlockShow-1.40/bin/ShowLDSVG -InPreFix /Data/home/ajklein/O_Files/O_AFR_LDBlock -OutPut /Data/home/ajklein/help.svg -SpeSNPName /Data/home/ajklein/O_Files/O_Snps_Positions.txt -crMiddle 217,160,182 -crEnd 220,57,119 -crTagSNP 0,0,0 -NumGradien 100 -ShowNum</pre>

__This is how Andrew learned Python, command line, LDBlockShow, and Plink to generate multiple LD Plots!!__

**Known Issues**<br>
For one of the O Snps in the EAS population (rs8176704), there is no data so the resulting LD Plot does not include this SNP. <br>
LD Calculations were compared to the [LD Matrix tool](https://ldlink.nci.nih.gov/?tab=ldmatrix).<br> This also shows the missing data for this SNP within this population.<br>
The LD calculations between the website and the program seem to be the same or similar in value.
