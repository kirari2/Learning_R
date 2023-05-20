Create sample dataframe `df`

```R
df <- data.frame(test_1 = c("Pass", "Fail", NA, "Pass"),
                 test_2 = c("Fail", "Fail", "Fail", "Pass"),
                 test_3 = c("Pass", NA, "Pass", "Fail"),
                 test_4 = c("Fail", "Pass", "Fail", "Fail"))
> df
  test_1 test_2 test_3 test_4
1   Pass   Fail   Pass   Fail
2   Fail   Fail   <NA>   Pass
3   <NA>   Fail   Pass   Fail
4   Pass   Pass   Fail   Fail
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
1   Pass   Fail   Pass   Fail
2   Fail   Fail   <NA>   Pass
3   <NA>   Fail   Pass   Fail
4   Pass   Pass   Fail   Fail

> t(df)
       [,1]   [,2]   [,3]   [,4]  
test_1 "Pass" "Fail" NA     "Pass"
test_2 "Fail" "Fail" "Fail" "Pass"
test_3 "Pass" NA     "Pass" "Fail"
test_4 "Fail" "Pass" "Fail" "Fail"

> print(match_table)
       test_1 test_2 test_3 test_4
test_1      3      2      1      0
test_2      2      4      0      2
test_3      1      0      3      1
test_4      0      2      1      4
```

As the upper and lower triangles are mirror images, only the upper triangle is printed
```R
> match_table[lower.tri(match_table)] <- NA
> print(match_table)
       test_1 test_2 test_3 test_4
test_1      3      2      1      0
test_2     NA      4      0      2
test_3     NA     NA      3      1
test_4     NA     NA     NA      4
```
