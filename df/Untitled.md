    BiocManager::install("Biobase")
    library(Biobase)

![](1.jpg) \#\# Download data

    con =url("http://bowtie-bio.sourceforge.net/recount/ExpressionSets/bodymap_eset.RData")
    load(file=con)
    close(con)
    bm = bodymap.eset
    edata = exprs(bm)

Solotion and anser
------------------

    row_sums = rowSums(edata)
    edata = edata[order(-row_sums),]
    index = 1:500
    heatmap(edata[index,],Rowv=NA,Colv=NA)

![](Untitled_files/figure-markdown_strict/unnamed-chunk-3-1.png)
