# Plant Compound KEGG Database  

![lifecycle](https://img.shields.io/badge/lifecycle-Experimental-lightcyan)
[![license](https://img.shields.io/badge/license-MIT-red)](https://opensource.org/licenses/MIT)
[![Myblog](https://img.shields.io/badge/Blog-ShanwLearnBioinfo-purple)](http://www.shawnlearnbioinfo.top/) 

--- 

## Introduction  

Kegg enrichment analysis of non-target metabolomics is a very difficult task for non-model plants. It is very important to  build kegg background files for non-model plants. This project will provide a Rscript for database construction of plants which can be found in kegg database. Besides, I will upload the database I create.

## How to build a database.

[KEGG API](https://www.kegg.jp/kegg/rest/keggapi.html) is a powerful tool to get what you want.

**Step1.** We can get the PathwayID to Pathway Name by `list` tool，such as http://rest.kegg.jp/list/pathway/zma provide maize kegg pathway information.


```html
# The rules:
http://rest.kegg.jp/list/pathway/ + abbreviation
# for Arabidopsis thaliana
http://rest.kegg.jp/list/pathway/ath
# for Gossypium hirsutum (upland cotton)
http://rest.kegg.jp/list/pathway/ghi
# for Zea mays (maize)
http://rest.kegg.jp/list/pathway/zma
```  

you can get the plant code you need in [KEGG Organisms: Complete Genomes](https://www.genome.jp/kegg/catalog/org_list.html)

The maize pathway id 2 pathway name table.

It is unpleasant to display the suffix (- Zea mays (maize)) in your analysis results. so I removed the suffix of pathway name. besides, the prefix of pathway name was also changed from path:zmaxxxxx to mapxxxxxx for step2.

```r
## raw table
Pathway ID	Pathway Name
path:zma00010	Glycolysis / Gluconeogenesis - Zea mays (maize)
path:zma00020	Citrate cycle (TCA cycle) - Zea mays (maize)
path:zma00030	Pentose phosphate pathway - Zea mays (maize)
path:zma00040	Pentose and glucuronate interconversions - Zea mays (maize)
path:zma00051	Fructose and mannose metabolism - Zea mays (maize)
path:zma00052	Galactose metabolism - Zea mays (maize)
path:zma00053	Ascorbate and aldarate metabolism - Zea mays (maize)
path:zma00061	Fatty acid biosynthesis - Zea mays (maize)
path:zma00062	Fatty acid elongation - Zea mays (maize)
path:zma00071	Fatty acid degradation - Zea mays (maize)
path:zma00073	Cutin, suberine and wax biosynthesis - Zea mays (maize)
path:zma00100	Steroid biosynthesis - Zea mays (maize)
path:zma00130	Ubiquinone and other terpenoid-quinone biosynthesis - Zea mays (maize)
path:zma00190	Oxidative phosphorylation - Zea mays (maize)
path:zma00195	Photosynthesis - Zea mays (maize)
path:zma00196	Photosynthesis - antenna proteins - Zea mays (maize)
path:zma00220	Arginine biosynthesis - Zea mays (maize)
path:zma00230	Purine metabolism - Zea mays (maize)
## clean table
Pathway ID	Pathway Name
map00010	Glycolysis / Gluconeogenesis
map00020	Citrate cycle (TCA cycle)
map00030	Pentose phosphate pathway
map00040	Pentose and glucuronate interconversions
map00051	Fructose and mannose metabolism
map00052	Galactose metabolism
map00053	Ascorbate and aldarate metabolism
map00061	Fatty acid biosynthesis
map00062	Fatty acid elongation
map00071	Fatty acid degradation
map00073	Cutin, suberine and wax biosynthesis
map00100	Steroid biosynthesis
map00130	Ubiquinone and other terpenoid-quinone biosynthesis
map00190	Oxidative phosphorylation
map00195	Photosynthesis
map00196	Photosynthesis - antenna proteins
map00220	Arginine biosynthesis
map00230	Purine metabolism
```
**Step2.** We can get the KEGG compound ID (CID) by link tools. eg. The compound which appeared in map00010 http://rest.kegg.jp/link/cpd/map00010. So we can all this information by the Pathway ID in first steps. You'd better use a web crawler to get the information automaticaly. I'll upload a web crawler written by r laterly.

```r
## raw table
Pathway ID	CID
path:map00010	cpd:C00022
path:map00010	cpd:C00024
path:map00010	cpd:C00031
path:map00010	cpd:C00033
path:map00010	cpd:C00036
path:map00010	cpd:C00068
path:map00010	cpd:C00074
path:map00010	cpd:C00084
path:map00010	cpd:C00103
path:map00010	cpd:C00111
path:map00010	cpd:C00118
path:map00010	cpd:C00186
path:map00010	cpd:C00197
path:map00010	cpd:C00221
path:map00010	cpd:C00236
path:map00010	cpd:C00267
path:map00010	cpd:C00469
path:map00010	cpd:C00631
path:map00010	cpd:C00668
path:map00010	cpd:C01159
path:map00010	cpd:C01172
path:map00010	cpd:C01451
path:map00010	cpd:C05125
path:map00010	cpd:C05345
path:map00010	cpd:C05378
path:map00010	cpd:C06186
path:map00010	cpd:C06187
path:map00010	cpd:C06188
path:map00010	cpd:C15972
path:map00010	cpd:C15973
path:map00010	cpd:C16255
## clean table
Pathway ID	CID
map00010	C00022
map00010	C00024
map00010	C00031
map00010	C00033
map00010	C00036
map00010	C00068
map00010	C00074
map00010	C00084
map00010	C00103
map00010	C00111
map00010	C00118
map00010	C00186
map00010	C00197
map00010	C00221
map00010	C00236
map00010	C00267
map00010	C00469
map00010	C00631
map00010	C00668
map00010	C01159
map00010	C01172
map00010	C01451
map00010	C05125
map00010	C05345
map00010	C05378
map00010	C06186
map00010	C06187
map00010	C06188
map00010	C15972
map00010	C15973
map00010	C16255
```

**Step3** Merge two table. We will get a `Pathway ID - CID - Pathway name`

## run kegg enrichment with clusterprofiler

`clusterprofiler::enricher()` could do enrichment analysis via a customized database, `GENE2TERM` and `TERM2NAME` could generated by the Step3 merged table. examples as follows:

```r
library(tidyverse)
library(clusterprofiler)
KEGG <- read.csv("query_file.csv")
head(KEGG)
 Query	KEGG
1 Adenosine monophosphate	C00020
2 L-Glutamic acid	C00025
3 L-ALANINE	C00041
4 L-Lysine	C00047
5 Lysine	C00047
6 L-ASPARTATE	C00049

## pathway analysis (not enrichment, just for compound-pathway relationship)
pathway_analysis = inner_join(KEGG,maize.db_final,by = c("KEGG" = "KEGGID")) %>% 
  mutate(
    Pathway = gsub(pattern = " - Zea mays \\(maize\\)",replacement = "",Pathway)
  ) %>% distinct()

## how many compounds in query file belongs to correspoding kegg pathway
Pathway_plt_data = pathway_analysis %>% 
  mutate(n = 1) %>% 
  group_by(PathwayID,Pathway) %>% 
  summarise(sum = sum(n)) %>% 
  arrange(desc(sum)) %>% 
  mutate(Pathway = factor(Pathway,levels = .$Pathway))

## barplot for kegg pathway analysis

ggplot()+
  geom_bar(data = Pathway_plt_data,
           mapping = aes(
             y = Pathway,
             x = sum,
             fill = log(sum)
           ),
           stat = "identity"
           )+
  geom_text(data = Pathway_plt_data,aes(label = sum,x = sum+1,y = Pathway,))+
  scale_fill_gradient(low = "blue",high = "red",guide = "colourbar")+
  theme_bw()+
  theme(
    axis.text.x = element_text(size = 10,face = "bold"),
    axis.text.y = element_text(size = 10,face = "bold"),
    axis.title.x = element_text(size = 12,face = "bold"),
    panel.border = element_rect(size = 1)
  )+
  ylab("")+xlab("Compound numbers")


## enrichment analysis

library(clusterProfiler)
t2g = data.frame(
  TERM = maize.db_final$PathwayID,
  GENE = maize.db_final$KEGGID
)
t2n = data.frame(
  TERM = maize.db_final$PathwayID,
  NAME = maize.db_final$Pathway
) %>% distinct()
head(KEGG)
eko <- enricher(
  gene = KEGG$KEGG,pvalueCutoff = 0.05,minGSSize = 3,maxGSSize = 500,qvalueCutoff = 1,TERM2GENE = t2g,TERM2NAME = t2n
)
barplot(eko,showCategory = 20)
dotplot(eko,showCategory = 20)
```

## Todo list

1. Rscript pipeline for database construction  
2. Shiny app and TBtools plugin.
3. Add more plant database
