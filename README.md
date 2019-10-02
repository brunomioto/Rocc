# Get data from speciesLink

Very preliminar functions to get occurrence data from [speciesLink API](http://api.splink.org.br) version beta 0.1. Courtesy of Sidnei de Souza :) 

Function is at R/spLink_url.R

Code to test the function bellow.

Output csv files are at results/

Installing and loading the package

```{r setup}
#devtools::install_github("saramortara/rspeciesLink")
devtools::load_all()
```

This package downloads information from the [speciesLink API](http://api.splink.org.br/). It generates the desired url and uses functions from `httr` and `readr` packages GET the url and save the output as a csv file. Request via POST is under development.   

## Example 1: basic search

Search for records of *Eugenia platyphylla* and *Chaetocalyx acutifolia* in speciesLink API. Same as: [http://api.splink.org.br/records/ScientificName/Eugenia%20platyphylla/Chaetocalyx%20acutifolia/scope/plants](http://api.splink.org.br/records/ScientificName/Eugenia%20platyphylla/Chaetocalyx%20acutifolia/scope/plants)

```{r}
sp1 <- "Eugenia platyphylla"
sp2 <- "Chaetocalyx acutifolia"
```

Setting scope `"plants"`. 

```{r}
ex01 <- spLink_url(filename = "ex01",
                   scientificname =  c(sp1, sp2),
                   Scope="plants")
```

Checking search output. 

```{r}
head(ex01$data)
dim(ex01$data)
str(ex01$data)
```

Checking if required species are in the output. 

```{r}
# especies requisitadas estao no banco
## lista de especies da busca
ex01.sp <- unique(ex01$data$scientificname)
## checando se as sp requisitadas estão no banco --> TRUE :)
c(sp1, sp2)%in%ex01.sp
```

Checking colnames in data output.

```{r eval=FALSE}
names(ex01$data)
```

```{r echo=FALSE}
knitr::kable(data.frame(columns=sort(names(ex01$data))))
```

## Example 2: specifying collection of origin and specimens with image

Search for *Rauvolfia selowii* and *Cantinoa althaeifolia*. Now using `collectioncode` and `Images` arguments.

Same as: [http://api.splink.org.br/records/CollectionCode/uec/scientificname/Rauvolfia%20sellowii/Cantinoa%20althaeifolia/Images/yes](http://api.splink.org.br/records/CollectionCode/uec/scientificname/Rauvolfia%20sellowii/Cantinoa%20althaeifolia/Images/yes)


```{r}
ex02 <- spLink_url(filename = "ex02",
                   CollectionCode = "uec",
                   scientificname = c("Rauvolfia sellowii", "Cantinoa althaeifolia"),
                   Images="Yes")
```

Checking again if species are in the search. 

```{r}
# de novo especies nao estao no output
c("Rauvolfia sellowii", "Cantinoa althaeifolia")%in%ex02$data$scientificname
```

Checking url used in the search. 

```{r}
ex02$url
# faz a busca
head(ex02$data)
dim(ex02$data)
str(ex02$data)
```

Is data only from UEC collection?

```{r}
# checando o campo collectioncode
unique(ex02$data$collectioncode)
```

## Example 3: testing coordinates quality selection

For species *Tillandsia stricta*. 

```{r}
ex03 <- spLink_url(filename = "ex03",
                   scientificname = "Tillandsia stricta",
                   Coordinates = "Yes",
                   coordinatesQuality = "Good")
```

Checking if species is in the output.

```{r}
"Tillandsia stricta"%in%ex03$data$scientificname
```

Checking url and output.

```{r}
ex03$url
# faz a busca
dim(ex03$data) # 1623
head(ex03$data)
```

Now with another selection of coordinate quality. 

```{r}
# outra selecao de qualidade de coordenadas
ex03b <- spLink_url(filename = "ex03b",
                   scientificname = "Tillandsia stricta")
                   #coordinatesQuality = "Bad")
```

Checking url and output.

```{r}
ex03b$url
# faz a busca
dim(ex03b$data) # 1762
head(ex03b$data)
```

## General check 

### Checking if files were writen on disk

Listing files on list. 

```{r}
list.files("results/", pattern = '.csv')
```


```{r}
a <- readr::read_csv("results/ex02.csv")
head(a)
```


