iteration and list columns
================

## Iteration

### Here’s some list

``` r
l = list(
  vec_numeric = 1:4,
  unif_sample = runif(100),
  mat = matrix(1:8, nrow =2 , ncol =4, byrow = TRUE),
  summary = summary(rnorm(1000))
)

l[['mat']]
```

    ##      [,1] [,2] [,3] [,4]
    ## [1,]    1    2    3    4
    ## [2,]    5    6    7    8

``` r
l[[4]]
```

    ##     Min.  1st Qu.   Median     Mean  3rd Qu.     Max. 
    ## -3.86477 -0.57133  0.05167  0.05568  0.72465  3.12405

Make a list that’s hopefully a bit more useful.

``` r
list_norms = 
  list(
    a = rnorm(20, 0, 5),
    b = rnorm(20, 4, 5),
    c = rnorm(20, 0, 10),
    d = rnorm(20, 4, 10)
  )

list_norms[['b']]
```

    ##  [1]  2.1843686  3.7316502  1.1546451  2.8313743 11.0432478  5.7938454
    ##  [7]  4.0250208 -0.9449351  5.1578291 10.1090393 -0.3718920  4.5205245
    ## [13]  5.1321408  6.0716081  8.6013174  6.1249606 -5.5382957  7.7737380
    ## [19]  2.0982104 13.9048629

Let’s reuse the funtion we wrote last time.

``` r
mean_and_sd = function(x) {
  
  if (!is.numeric(x)) {
    stop("Argument x should be numeric")
  } else if (length(x) == 1) {
    stop("Cannot be computed for length 1 vectors")
  }
  
  mean_x = mean(x)
  sd_x = sd(x)

  out_df = 
    tibble(
      mean = mean_x, 
      sd = sd_x
    )
  return (out_df)
}
```

Let’s use the function to take mean and sd of all samples.

``` r
mean_and_sd(list_norms[["a"]])
```

    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.786  5.04

``` r
mean_and_sd(list_norms[["b"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.67  4.46

``` r
mean_and_sd(list_norms[["c"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.22  12.7

``` r
mean_and_sd(list_norms[["d"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.13  11.3

## Use a for loop

Create output list, and run a for loop

``` r
output = vector("list", length = 4)

for (i in 1:4) {
  
  output[[i]] = mean_and_sd(list_norms[[i]])
  
}

output
```

    ## [[1]]
    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.786  5.04
    ## 
    ## [[2]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.67  4.46
    ## 
    ## [[3]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.22  12.7
    ## 
    ## [[4]]
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.13  11.3

## Do the same thing

but with `map` instead

``` r
#map(list,function)
output = map(list_norms, mean_and_sd)
#equivalent to the for loop above
```

let’s do a couple of other things

``` r
output = map(list_norms, mean_and_sd)
#outputs in individual df

output = map_dfr(list_norms, mean_and_sd)
#simplifies the output into a more reader-friendly table

output = map_dbl(list_norms, IQR)
#simplifies the output into a more reader-friendly list
```

## List columns!!

``` r
## easier organizational structure
listcol_df = 
  tibble(
    name = c("a", "b", 'c', "d"),
    samp = list_norms
  )

## can pipe easily as it is a dataframe structure
listcol_df %>% 
  filter(name %in% c("a","b"))
```

    ## # A tibble: 2 × 2
    ##   name  samp        
    ##   <chr> <named list>
    ## 1 a     <dbl [20]>  
    ## 2 b     <dbl [20]>

``` r
listcol_df %>% 
  select(-samp)
```

    ## # A tibble: 4 × 1
    ##   name 
    ##   <chr>
    ## 1 a    
    ## 2 b    
    ## 3 c    
    ## 4 d

``` r
##This is also a list at the same time; can pull out columns of interest using list syntax
listcol_df[["samp"]][["a"]]
```

    ##  [1]  0.53121781  3.30683950 -6.65649458 -0.25380763 -3.64183307 -7.11280036
    ##  [7] -8.80607443 -2.85115521 -3.57420437  5.88449431  1.15787509  1.82199504
    ## [13]  4.53446339  9.53006212  0.04018126  1.11822193 -0.32475395 -6.67651878
    ## [19]  4.18463312 -7.92829856

Compute mean and sd

``` r
mean_and_sd(listcol_df[["samp"]][["a"]])
```

    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.786  5.04

``` r
mean_and_sd(listcol_df[["samp"]][["b"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.67  4.46

``` r
mean_and_sd(listcol_df[["samp"]][["c"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.22  12.7

``` r
mean_and_sd(listcol_df[["samp"]][["d"]])
```

    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.13  11.3

``` r
map(listcol_df[['samp']], mean_and_sd)
```

    ## $a
    ## # A tibble: 1 × 2
    ##     mean    sd
    ##    <dbl> <dbl>
    ## 1 -0.786  5.04
    ## 
    ## $b
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  4.67  4.46
    ## 
    ## $c
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1 -3.22  12.7
    ## 
    ## $d
    ## # A tibble: 1 × 2
    ##    mean    sd
    ##   <dbl> <dbl>
    ## 1  1.13  11.3

ADD a list column!!

``` r
listcol_df %>% 
  mutate(
    output = map(samp, mean_and_sd),
    iqr = map_dbl(samp, IQR)
  )
```

    ## # A tibble: 4 × 4
    ##   name  samp         output             iqr
    ##   <chr> <named list> <named list>     <dbl>
    ## 1 a     <dbl [20]>   <tibble [1 × 2]>  6.59
    ## 2 b     <dbl [20]>   <tibble [1 × 2]>  4.37
    ## 3 c     <dbl [20]>   <tibble [1 × 2]> 16.9 
    ## 4 d     <dbl [20]>   <tibble [1 × 2]> 12.1

``` r
listcol_df %>% 
  mutate(
    output = map(samp, mean_and_sd),
    iqr = map_dbl(samp, IQR)
  ) %>% 
  select(-samp) %>% 
  unnest(output)
```

    ## # A tibble: 4 × 4
    ##   name    mean    sd   iqr
    ##   <chr>  <dbl> <dbl> <dbl>
    ## 1 a     -0.786  5.04  6.59
    ## 2 b      4.67   4.46  4.37
    ## 3 c     -3.22  12.7  16.9 
    ## 4 d      1.13  11.3  12.1

### NSDUH dataset

output a df by extracting df columns 1,4,5, and put everything into a
single df

This is a version of our function las time.

``` r
nsduh_table_format = function (html, table_num){
  out_table = 
    html |> 
    html_table() |> 
    nth(table_num) |>
    slice(-1) |> 
    select(-contains("P Value"))
  
  return(out_table)
}
```

We need to import html and then extract the corret tables.

``` r
nsduh_url = "http://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

nsduh_html = read_html(nsduh_url)
```

``` r
nsduh_table_format(nsduh_html, table_num = 1)
```

    ## # A tibble: 56 × 11
    ##    State `12+(2013-2014)` `12+(2014-2015)` `12-17(2013-2014)` `12-17(2014-2015)`
    ##    <chr> <chr>            <chr>            <chr>              <chr>             
    ##  1 Tota… 12.90a           13.36            13.28b             12.86             
    ##  2 Nort… 13.88a           14.66            13.98              13.51             
    ##  3 Midw… 12.40b           12.76            12.45              12.33             
    ##  4 South 11.24a           11.64            12.02              11.88             
    ##  5 West  15.27            15.62            15.53a             14.43             
    ##  6 Alab… 9.98             9.60             9.90               9.71              
    ##  7 Alas… 19.60a           21.92            17.30              18.44             
    ##  8 Ariz… 13.69            13.12            15.12              13.45             
    ##  9 Arka… 11.37            11.59            12.79              12.14             
    ## 10 Cali… 14.49            15.25            15.03              14.11             
    ## # ℹ 46 more rows
    ## # ℹ 6 more variables: `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `18+(2013-2014)` <chr>,
    ## #   `18+(2014-2015)` <chr>

``` r
nsduh_table_format(nsduh_html, table_num = 4)
```

    ## # A tibble: 56 × 11
    ##    State `12+(2013-2014)` `12+(2014-2015)` `12-17(2013-2014)` `12-17(2014-2015)`
    ##    <chr> <chr>            <chr>            <chr>              <chr>             
    ##  1 Tota… 1.66a            1.76             0.60               0.64              
    ##  2 Nort… 1.94a            2.18             0.60               0.66              
    ##  3 Midw… 1.37             1.43             0.48               0.54              
    ##  4 South 1.45b            1.56             0.53               0.57              
    ##  5 West  2.03             2.05             0.82               0.85              
    ##  6 Alab… 1.23             1.22             0.42               0.41              
    ##  7 Alas… 1.54a            2.00             0.51               0.65              
    ##  8 Ariz… 2.25             2.29             1.01               0.85              
    ##  9 Arka… 0.93             1.07             0.41               0.48              
    ## 10 Cali… 2.14             2.16             0.89               0.94              
    ## # ℹ 46 more rows
    ## # ℹ 6 more variables: `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `18+(2013-2014)` <chr>,
    ## #   `18+(2014-2015)` <chr>

``` r
nsduh_table_format(nsduh_html, table_num = 5)
```

    ## # A tibble: 56 × 11
    ##    State `12+(2013-2014)` `12+(2014-2015)` `12-17(2013-2014)` `12-17(2014-2015)`
    ##    <chr> <chr>            <chr>            <chr>              <chr>             
    ##  1 Tota… 0.30             0.33             0.12               0.10              
    ##  2 Nort… 0.43a            0.54             0.13               0.13              
    ##  3 Midw… 0.30             0.31             0.11               0.10              
    ##  4 South 0.27             0.26             0.12               0.08              
    ##  5 West  0.25             0.29             0.13               0.11              
    ##  6 Alab… 0.22             0.27             0.10               0.08              
    ##  7 Alas… 0.70a            1.23             0.11               0.08              
    ##  8 Ariz… 0.32a            0.55             0.17               0.20              
    ##  9 Arka… 0.19             0.17             0.10               0.07              
    ## 10 Cali… 0.20             0.20             0.13               0.09              
    ## # ℹ 46 more rows
    ## # ℹ 6 more variables: `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `18+(2013-2014)` <chr>,
    ## #   `18+(2014-2015)` <chr>

``` r
nsduh_df = 
  tibble(
    drug = c("marj", "cocaine", "heroine"),
    table_num = c(1,4,5)
  ) %>% 
  mutate(
    table = map(table_num, nsduh_table_format, html = nsduh_html)
  ) %>% 
  unnest(table)
                                            
nsduh_df %>% 
  filter(State == "New York")
```

    ## # A tibble: 3 × 13
    ##   drug    table_num State   `12+(2013-2014)` `12+(2014-2015)` `12-17(2013-2014)`
    ##   <chr>       <dbl> <chr>   <chr>            <chr>            <chr>             
    ## 1 marj            1 New Yo… 14.24b           15.04            13.94             
    ## 2 cocaine         4 New Yo… 2.28             2.54             0.71              
    ## 3 heroine         5 New Yo… 0.38a            0.52             0.13              
    ## # ℹ 7 more variables: `12-17(2014-2015)` <chr>, `18-25(2013-2014)` <chr>,
    ## #   `18-25(2014-2015)` <chr>, `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>
