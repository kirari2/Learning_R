Create sample dataframe `df`

```R
df <- data.frame(test_1 = c("Pass", NA, NA, "Pass"),
                 test_2 = c(NA, NA, "Pass", "Pass"),
                 test_3 = c("Pass", NA, "Pass", NA),
                 test_4 = c("Pass", "Pass", NA, NA))
> df
  test_1 test_2 test_3 test_4
1   Pass   <NA>   Pass   Pass
2   <NA>   <NA>   <NA>   Pass
3   <NA>   Pass   Pass   <NA>
4   Pass   Pass   <NA>   <NA>
```

Initialize an empty match table with the same column and row names
```R
column_names <- colnames(df)
match_table <- matrix(0,
                      nrow = nrow(df),
                      ncol = ncol(df),
                      dimnames = list(column_names, column_names))
```

Compare each cell with other cells and calculate the number of matches
```R
for (i in 1:ncol(df)) {
  for (j in 1:ncol(df)) {
    for (k in 1:nrow(df)) {
      if (!is.na(df[k, i]) && 
          !is.na(df[k, j]) &&
          identical(df[k, i], df[k, j])) {
            match_table[i, j] <- match_table[i, j] + 1
      }
    }
  }
}
```

Print the match table
```R
> df
  test_1 test_2 test_3 test_4
1   Pass   <NA>   Pass   Pass
2   <NA>   <NA>   <NA>   Pass
3   <NA>   Pass   Pass   <NA>
4   Pass   Pass   <NA>   <NA>

> t(df)
       [,1]   [,2]   [,3]   [,4]  
test_1 "Pass" NA     NA     "Pass"
test_2 NA     NA     "Pass" "Pass"
test_3 "Pass" NA     "Pass" NA    
test_4 "Pass" "Pass" NA     NA 

> match_table
       test_1 test_2 test_3 test_4
test_1      2      1      1      1
test_2      1      2      1      0
test_3      1      1      2      1
test_4      1      0      1      2
```

As the upper and lower triangles are mirror images, only the upper triangle is printed
```R
> match_table[lower.tri(match_table)] <- NA
> match_table
       test_1 test_2 test_3 test_4
test_1      2      1      1      1
test_2     NA      2      1      0
test_3     NA     NA      2      1
test_4     NA     NA     NA      2
```
