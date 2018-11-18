
# Functional enrichment for gene lists
These functions are a collection of wrappers designed to streamline functional enrichment analysis for multiple gene lists. The wrappers are using the base WebGestaltR package that connects to in-browser WebGestalt tool to perform the enrichments.

To run enrichment, install addR - contains all relevant functions, including dependencies



```
devtools::install_github("ks4471/addR")
library(adds)
```

###   WebGestalt using ORA (over-representation analysis)


Input is expected as a list of named gene sets, enrichment performed for each gene set. Example gene list available to download from dropbox https://www.dropbox.com/s/y2szo7ywloleh92/example.gene.list.Rdata?dl=0
```
geist=list()
Load('example.gene.list.Rdata')	  							##  named list of character vectors (1 per gene set)
for(iset in names(input_gene_sets)){
	lprogr(iset,names(input_gene_sets),T)						##  optional progress tracker
	geist[[iset]]=webg(								##  main wrapper funciton to connect to "WebGestalt" server, requires "WebgestaltR" package
		genset=input_gene_sets[[iset]]						##  character list of gene_ids to perform enrichment with
		,genbkg=bkg_genes					 		##  backround genes, ie genes used to perform the analysis that yielded "genset" above, eg, all expressed in the assay/experiment
		,idtype='ensembl_gene_id' 						##  idtype - same for geneset and genbkg, assume that the user is using well mapped & related gene sets. typically used: "genesymbol","ensembl_gene_id","entrezgene"
		,enrichtype = c("go","pathw", "other") 					##  can specify general categories or use list of specific enrichment terms (as per website / within function code)
		,organism = "hsapiens"
		,quick=T 								##  limit to 4 pre-selected terms geneontology_Biological_Process_noRedundant, pathway_KEGG, pathway_Reactome, disease_Disgenet
		)
}
```

###   WebGestalt using GSEA (rank based gene set enrichment analysis)
```
geist=list()
# list_gene_sets  								##  named list of character vectors (1 per gene set)
for(iset in names(rnkg)){
	lprogr(iset,names(rnkg),T)						##  optional progress tracker
	geist[[iset]]=webg(							##  main wrapper funciton to connect to "WebGestalt" server, requires "WebgestaltR" package
		genset=rnkg[[iset]]						##  character list of gene_ids to perform enrichment with
		# ,genbkg=unique(unlist(rnkg)) 					##  backround genes, ie genes used to perform the analysis that yielded "genset" above, eg, all expressed in the assay/experiment
		,idtype='genesymbol' 						##  idtype - same for geneset and genbkg, assume that the user is using well mapped & related gene sets. typically used: "genesymbol","ensembl_gene_id","entrezgene"
		,organism='mmusculus'						##  organism - currently expects only hsapiens or mmusculus, some things may break otherwise
		# ,enrichtype = c("go") #,"pathw", "other") 			##  can specify general categories or use list of specific enrichment terms (as per website / within function code)
		,quick=T 							##  limit to 4 pre-selected terms geneontology_Biological_Process_noRedundant, pathway_KEGG, pathway_Reactome, disease_Disgenet
		,method='GSEA'
		,outdir='~/Dropbox/PROJ/itac/coms/dtb/tmp/'
		# ,fdrThr=0.1
		)
}
```


###   Re-format the output and make plots

```
pdat=webg.plot(geist)									##  collate webg() ouput for multiple gene sets
	Head(pdat)

for(ityp in names(pdat)){
	 lprogr(ityp,names(pdat))
	pdf(paste0('output_path/name_geneset.',ityp,'.pdf'),height=22,width=15)		##  "gestaltheat" function is designed to auto-fit to these dimensions ()
		gestaltheat(pdat[[ityp]])						##  plot the collated data as Heatmap
	dev.off()
	
	write.file(pdat[[ityp]],file=paste0('output_path/name_geneset.',ityp,'.txt'))  ##   save plot data as .txt
	
}

```
folder containing example output https://www.dropbox.com/sh/snpmttmgjw14e5j/AAABAI8B6-VQfPv8-xwG5JJSa?dl=0



## Functional database
http://www.webgestalt.org/option.php

## Base R package
https://cran.r-project.org/web/packages/WebGestaltR/index.html

