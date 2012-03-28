
<!-- Uses knitcitations -->
*This is a work in progress*

% Rfishbase
% Carl Boettiger[^\*][^a] and Peter Wainwright[^a]
% 

[^a]: Center for Population Biology, University of California, Davis, United  States
  

# Abstract 
We introduce a package that provides interactive and programmatic
access to the FishBase repository. This package allows us to
interact with data on over 30,000 fish species in the rich
statistical computing environment, `R`. We illustrate how this
direct, scriptable interface to FishBase data enables better
discovery and integration essential for large-scale comparative
analyses. We provide several examples to illustrate how the package
works, and how it can be integrated into such as phylogenetics
packages `ape` and `geiger`.


###### keywords 
R | vignette | fishbase

# Introduction




```
Error: unable to open file to read
```





Informatics
Stuff about Machine access to data, role of large scale data in ecology 

```

Error in lapply(X = X, FUN = FUN, ...) : object 'bib' not found

```



FishBase ([fishbase.org](http://fishbase.org)) is an award-winning
online database of information about the morphology, trophic ecology,
physiology, ecotoxicology, reproduction, economic relevance of the
world’s fish, organized by species 

```

Error in lapply(X = X, FUN = FUN, ...) : object 'bib' not found

```

. FishBase was developed in
collaboration with the United Nations Food and Agriculture Organization
and is supported by a consortium of nine research institutions. In
addition to its web-based interface, FishBase provides machine readable
XML files for 30622 of its species entries.

To facilitate the extraction, visualization, and integration of this
data in research, we have written the `rfishbase` package for the R
language for statistical computing and graphics 

```

Error in parse(text = code[i]) : 1:11: unexpected INCOMPLETE_STRING
1: citep(bib["rteam2012}])
             ^

```

. R is a freely
available open source computing environment that is used extensively in
ecological research, with a large library of packages built explicitly
for this purpose 

```

Error in lapply(X = X, FUN = FUN, ...) : object 'bib' not found

```

.



# A programmatic interface

The `rfishbase` package works by creating a cached copy of all data on
fishbase currently available in XML format. Caching increases the speed
of queries and places minimal demands on the fishbase server, which in
it’s present form is not built to support direct access to application
programming interfaces (APIs). The cached copy can be loaded in to R
using the command:



```r
data(fishbase) 
```




To get the most recent copy of fishbase, update the cache instead. The
update may take up to 24 hours. This copy is stored in the working
directory with the current date and can be loaded when finished.



```r
updateCache()
loadCache("2012-03-26fishdata.Rdat")
```



Loading the database creates an object called fish.data, with one entry
per fish species for which data was successfully found, for a total of
30622 species.  

# Data extraction, analysis, and visualization

Comparative studies

The examples we give here are meant to be illustrative of the kinds of
queries that are possible, and also provide a simple introduction to
assist the reader in using the software itself.

Quantitatve queries 



```r
yr <- getSize(fish.data, "age")
```




Identify fish with mention of noturnal behavior in the their trophic description.


```r
nocturnal <- which_fish("nocturnal", "trophic", fish.data)
```



The object returned is a list of true/false values,
indicating all fish in the dataset that match this query. This format is
useful because it allows us to subset the original data and pass it to
another query. For instance, we can use the `fish_names` function to
return the names of these fish. `fish_names` can return more than just
species names – here we ask it to give us the top taxonomic Orders of
these nocturnal fish, organized into a table:



```r
nocturnal_orders <-fish_names(fish.data[nocturnal], "Order") 
xtable(table(nocturnal_orders))
```



```
<!-- html table generated in R 2.14.2 by xtable 1.7-0 package -->
<!-- Tue Mar 27 17:50:42 2012 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> nocturnal_orders </TH>  </TR>
  <TR> <TD align="right"> Anguilliformes </TD> <TD align="right">   4 </TD> </TR>
  <TR> <TD align="right"> Atheriniformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Aulopiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Beloniformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Beryciformes </TD> <TD align="right">  10 </TD> </TR>
  <TR> <TD align="right"> Clupeiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Elopiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Gadiformes </TD> <TD align="right">   2 </TD> </TR>
  <TR> <TD align="right"> Gymnotiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Heterodontiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Myctophiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Myxiniformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Ophidiiformes </TD> <TD align="right">   2 </TD> </TR>
  <TR> <TD align="right"> Orectolobiformes </TD> <TD align="right">   2 </TD> </TR>
  <TR> <TD align="right"> Osmeriformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Osteoglossiformes </TD> <TD align="right">   1 </TD> </TR>
  <TR> <TD align="right"> Perciformes </TD> <TD align="right">  56 </TD> </TR>
  <TR> <TD align="right"> Scorpaeniformes </TD> <TD align="right">   3 </TD> </TR>
  <TR> <TD align="right"> Siluriformes </TD> <TD align="right">  10 </TD> </TR>
  <TR> <TD align="right"> Tetraodontiformes </TD> <TD align="right">   2 </TD> </TR>
  <TR> <TD align="right"> Torpediniformes </TD> <TD align="right">   1 </TD> </TR>
   </TABLE>
```




The real power of programatic access the ease with which we can combine,
visualize, and statistically test a custom compilation of this data. We
begin by generating a custom data table of characteristics of interest




```r
reef <- which_fish("reef", "habitat", fish.data)
nocturnal <- which_fish("nocturnal", "trophic", fish.data)
marine <- which_fish("marine", "habitat", fish.data)
africa <- which_fish("Africa:", "distribution", fish.data)
age <- getSize(fish.data, "age")
length <- getSize(fish.data, "length")
order <- fish_names(fish.data, "Order")
dat <- data.frame(reef, nocturnal,  marine,  africa, age, length, order)
```




This data frame contains categorical data (*e.g.* is the fish a
carnivore) and continuous data (*e.g.* weight or age of fish). We can
take advantage of the rich data visualization in R to begin exploring
this data.


```r
ggplot(dat,aes(age, length, color=marine)) + geom_point(position='jitter',alpha=.8) + scale_y_log10() + scale_x_log10() 
```

![plot of chunk dataplots](http://farm8.staticflickr.com/7198/7022507425_1a834f5f4f_o.png) 

 More nocturnal species are found on reefs than non-reefs


```r
qplot(reef[nocturnal])
```

![plot of chunk unnamed-chunk-1](http://farm8.staticflickr.com/7096/7022507569_484f24fabe_o.png) 

Are reef species longer lived than non-reef species in the marine environment?


```r
ggplot(subset(dat, marine),aes(reef, log(age))) + geom_boxplot() 
```

![plot of chunk unnamed-chunk-2](http://farm8.staticflickr.com/7095/6876405846_cc241a58d0_o.png) 



Fraction of marine species found in the 10 largest orders


```r
biggest <- names(head(sort(table(order),decr=T), 10))
ggplot(subset(dat,order %in% biggest), aes(order, fill=marine)) + geom_bar() 
```

![plot of chunk fraction_marine](http://farm7.staticflickr.com/6216/6876406010_1fd2688496_o.png) 

Typical use of the package involves constructing queries to identify
species matching certain criteria. The powerful R interface makes it
easy to combine queries in complex ways to answer particular questions.
For instance, we can ask "are there more labrids or goby species of reef
fish?" using the following queries:

Get all species in fishbase from the families "Labridae" (wrasses) or
"Scaridae" (parrotfishes):


```r
labrid <- familySearch("(Labridae|Scaridae)", fish.data)
```



and get all the species of gobies


```r
goby <- familySearch("Gobiidae", fish.data)
```



Identify how many labrids are found on reefs


```r
labrid.reef <- habitatSearch("reef", fish.data[labrid])
nlabrids <- sum(labrid.reef)
```



and how many gobies are found on reefs:


```r
ngobies <- sum (habitatSearch("reef", fish.data[goby]) )
```



showing us that there are 503 labrid species associated with reefs,
and 401 goby species associated with reefs.  




Note that any function can take a subset of the data, as specified by
the square brackets.

# Integration of analyses

One of the greatest advantages about accessing FishBase directly through
R is the ability to take advantage of the suite of specialized analyses
available through R packages. Likewise, users familiar with these
packages can more easily take advantage of the data available on
fishbase. We illustrate this with an example that combines phylogenetic
methods available in R with quantitative trait data available from
`rfishbase`.

This series of commands illustrates testing for a phylogenetically
corrected correlation between the maximum observed size of a species and
the maximum observed depth at which it is found.


load a phylogenetic tree and some phylogenetics packages


```r
data(labridtree)
require(geiger) 
```




 Find those species on FishBase 


```r
myfish <- findSpecies(tree$tip.label, fish.data)
```




Get the maxium depth of each species and sizes of each species: 


```r
depths <- getDepth(fish.data[myfish])[,"deep"]
size <- getSize(fish.data[myfish], "length")
```




Drop tips from the phylogeny for unmatched species.  


```r
data <- na.omit(data.frame(size,depths))
pruned <- treedata(tree, data)
```



```
Dropped tips from the tree because there were no matching names in the data:
 [1] "Anampses_geographicus"     "Bodianus_perditio"        
 [3] "Chlorurus_bleekeri"        "Choerodon_cephalotes"     
 [5] "Choerodon_venustus"        "Coris_batuensis"          
 [7] "Diproctacanthus_xanthurus" "Halichoeres_melanurus"    
 [9] "Halichoeres_miniatus"      "Halichoeres_nigrescens"   
[11] "Macropharyngodon_choati"   "Oxycheilinus_digrammus"   
[13] "Scarus_flavipectoralis"    "Scarus_rivulatus"         

```





Use phylogenetically independent contrasts [@felsenstein1985] to determine if depth correlates with size after correcting for phylogeny:



```r
x <- pic(pruned$data[["size"]],pruned$phy)
y <- pic(pruned$data[["depths"]],pruned$phy)
xtable(summary(lm(y ~ x - 1)))
```



```
<!-- html table generated in R 2.14.2 by xtable 1.7-0 package -->
<!-- Tue Mar 27 17:51:15 2012 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> Estimate </TH> <TH> Std. Error </TH> <TH> t value </TH> <TH> Pr(&gt |t|) </TH>  </TR>
  <TR> <TD align="right"> x </TD> <TD align="right"> 0.0713 </TD> <TD align="right"> 0.0993 </TD> <TD align="right"> 0.72 </TD> <TD align="right"> 0.4744 </TD> </TR>
   </TABLE>
```



```r
ggplot(data.frame(x=x,y=y), aes(x,y)) + geom_point() + stat_smooth(method=lm)
```

![plot of chunk unnamed-chunk-7](http://farm8.staticflickr.com/7277/7022508185_916d333ac4_o.png) 


We can also estimate different evolutionary models for these traits to decide which best describes the data,



```r
bm <- fitContinuous(pruned$phy, pruned$data[["depths"]], model="BM")[[1]]
ou <- fitContinuous(pruned$phy, pruned$data[["depths"]], model="OU")[[1]]
```




where the Brownian motion model has an AIC score of 1185 while
the OU model has a score
of 918, suggesting that OU is the better model.

In a similar fashion, programmers of other R software packages can make
use of the rfishbase package to make this data available to their
functions, further increasing the use and impact of fishbase. For
instance, the project OpenFisheries.org makes use of the fishbase
package to provide information about commercially relevant species.

# Discussion

## The self-updating study

Describe how this package could help make studies that could be
automatically updated as the dataset is improved and expanded (like the
examples in this document which are automatically run when the pdf is
created). 


```

Error in lapply(X = X, FUN = FUN, ...) : object 'bib' not found

```

.


## Limitations and future directions

Fishbase contains much additional data that has not been made accessible
in it’s machine-readable XML format. We are in contact with the database
managers and look forward to providing access to additional types of
data as they become available.

# Acknowledgements

CB is supported by a Computational Sciences Graduate Fellowship from the
Department of Energy under grant number DE-FG02-97ER25308.


[^\*]: Corresponding author <cboettig@ucdavis.edu> 


# References


```r
bibliography()
```




