Create sample dataframe `df`

```R
df <- data.frame(
  column1 = c("A", "B", NA, "D"),
  column2 = c("A", "B", "D", "E"),
  column3 = c("B", NA, "C", "E"),
  column4 = c("A", "B", "C", "D")
)
```

`df` will look like this:
```R
> df
  column1 column2 column3 column4
1       A       A       B       A
2       B       B    <NA>       B
3    <NA>       D       C       C
4       D       E       E       D
```

Create column and row names
```R
column_names <- colnames(df)
```

Initialize an empty match table
```R
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
> print(match_table)
        column1 column2 column3 column4
column1       3       2       0       3
column2       2       4       1       2
column3       0       1       3       1
column4       3       2       1       4
```

As the upper and lower triangles are mirror images, only the upper triangle is printed
```R
> match_table[lower.tri(match_table)] <- NA
> print(match_table)
        column1 column2 column3 column4
column1       3       2       0       3
column2      NA       4       1       2
column3      NA      NA       3       1
column4      NA      NA      NA       4
```
