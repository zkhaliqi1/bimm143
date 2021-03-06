class07
================
Zoheb Khaliqi
1/28/2020

These are functions here, look here

``` r
#Functions

is_both_na <- function(x,y) {
  if(length(x) != length(y)) {
    stop("your inputs aren't the same length")
  }
  is.na(x) & is.na(y)
}

rescale_new <- function(x, y) {
  ## Print some info on where NA's are as well as the number of them 
  if(length(x) != length(y)) {
    stop("Input x and y should be vectors of the same length", call.=FALSE)
  }
  na.in.both <- ( is.na(x) & is.na(y) )
  na.number  <- sum(na.in.both)
  na.which   <- which(na.in.both)

  message("Found ", na.number, " NA's at position(s):", 
          paste(na.which, collapse=", ") ) 
  
  return( list(number=na.number, which=na.which) )
}



rescale <- function(x, na.rm=TRUE, plot=FALSE, ...) {
  # Our rescale function from the end of lecture 6
  if( !is.numeric(x) ) {
    stop("Input x should be numeric", call.=FALSE)
  }

  if(na.rm) {
    rng <-range(x, na.rm=TRUE)
  } else {
    rng <-range(x)
  }

  answer <- (x - rng[1]) / (rng[2] - rng[1])
  if(plot) { 
    plot(answer, ...) 
  }

  return(answer)
}
```

``` r
x <- c(1, 2, NA, 3, NA)
y <- c(NA, 3, NA, 3, 4)
```

``` r
# Start with a simple version of the problem
df1 <- data.frame(IDs=c("gene1", "gene2", "gene3"),
 exp=c(2,1,1),
 stringsAsFactors=FALSE)
df2 <- data.frame(IDs=c("gene2", "gene4", "gene3", "gene5"),
 exp=c(-2, NA, 1, 2),
 stringsAsFactors=FALSE)
```

``` r
x <- df1$IDs
y <- df2$IDs

intersect(x,y) #See where both share same 
```

    ## [1] "gene2" "gene3"

``` r
x %in% y #Takes all of x and looks at which elements of x are in y
```

    ## [1] FALSE  TRUE  TRUE

``` r
x #Way to subset
```

    ## [1] "gene1" "gene2" "gene3"

``` r
x[x %in% y] #Allows to show which elements they intersect at (specific); just taking the TRUE
```

    ## [1] "gene2" "gene3"

``` r
y
```

    ## [1] "gene2" "gene4" "gene3" "gene5"

``` r
y%in%x
```

    ## [1]  TRUE FALSE  TRUE FALSE

``` r
y[y%in%x] #Same but with respect to y
```

    ## [1] "gene2" "gene3"

``` r
cbind(x[x %in% y], y[y%in%x]) #Binds the vectors together; can use to make a function
```

    ##      [,1]    [,2]   
    ## [1,] "gene2" "gene2"
    ## [2,] "gene3" "gene3"

``` r
# Making first snippet into first function
gene_intersect <- function(x,y) {
  cbind(x[x %in% y], y[y%in%x])
}
gene_intersect(x,y)
```

    ##      [,1]    [,2]   
    ## [1,] "gene2" "gene2"
    ## [2,] "gene3" "gene3"

# Lets edit to take input data frames

``` r
gene_intersect2 <- function(df1, df2) {
  cbind( df1[df1$IDs %in% df2$IDs,],
         df2[df2$IDs %in% df1$IDs, "exp"])
}
gene_intersect2(df1, df2)
```

    ##     IDs exp df2[df2$IDs %in% df1$IDs, "exp"]
    ## 2 gene2   1                               -2
    ## 3 gene3   1                                1

``` r
# Make sure everything is how we expect
gene.colname="IDs"
df1[, gene.colname]
```

    ## [1] "gene1" "gene2" "gene3"

``` r
# Add df1[,gene.colname] to function
gene_intersect3 <- function(df1, df2, gene.colname="IDs") {
  cbind( df1[ df1[,gene.colname] %in%
 df2[,gene.colname], ],
 exp2=df2[ df2[,gene.colname] %in%
 df1[,gene.colname], "exp"] )
}
gene_intersect3(df1, df2)
```

    ##     IDs exp exp2
    ## 2 gene2   1   -2
    ## 3 gene3   1    1

``` r
# Simplify even more 
gene_intersect4 <- function(df1, df2, gene.colname="IDs") {
  df1.name <- df1[,gene.colname]
 df2.name <- df2[,gene.colname]

 df1.inds <- df1.name %in% df2.name
 df2.inds <- df2.name %in% df1.name

 cbind( df1[ df1.inds, ],
 exp2=df2[ df2.inds, "exp"] )
}
gene_intersect4(df1, df2)
```

    ##     IDs exp exp2
    ## 2 gene2   1   -2
    ## 3 gene3   1    1

``` r
df1 <- data.frame(IDs=c("gene1", "gene2", "gene3"),
 exp=c(2,1,1),
 stringsAsFactors=FALSE)
df3 <- data.frame(IDs=c("gene2", "gene2", "gene5", "gene5"),
 exp=c(-2, NA, 1, 2),
 stringsAsFactors=FALSE)
gene_intersect4(df1, df3)
```

    ## Warning in data.frame(..., check.names = FALSE): row names were found from a
    ## short variable and have been discarded

    ##     IDs exp exp2
    ## 1 gene2   1   -2
    ## 2 gene2   1   NA

``` r
merge(df1, df2, by="IDs")
```

    ##     IDs exp.x exp.y
    ## 1 gene2     1    -2
    ## 2 gene3     1     1
