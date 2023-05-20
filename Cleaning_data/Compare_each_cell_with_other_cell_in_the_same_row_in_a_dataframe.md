## Compare each cell with other cells of the same row in a data frame

The following is the R codes trying to solve the problem of"
- Comparing each cell with the other cells of the same row in a data frame
- Record the number of matches in a cross-tabulation of the column names, i.e. row and column are the same in the matrix
- Since the upper and lower triangles of the matrix are the same, only the upper part is shown  

\
(1) Create sample dataframe `df`
```R
df <- data.frame(test_1 = c("Pass", NA, NA, "Pass"),
                 test_2 = c(NA, NA, "Pass", "Pass"),
                 test_3 = c("Pass", NA, "Pass", NA),
                 test_4 = c("Pass", "Pass", NA, NA))
> df
#   test_1 test_2 test_3 test_4
# 1   Pass   <NA>   Pass   Pass
# 2   <NA>   <NA>   <NA>   Pass
# 3   <NA>   Pass   Pass   <NA>
# 4   Pass   Pass   <NA>   <NA>
```
\
(2) Initialize an empty match table with the same column and row names
```R
column_names <- colnames(df)
match_table <- matrix(0,
                      nrow = nrow(df),
                      ncol = ncol(df),
                      dimnames = list(column_names, column_names))
```
\
(3) Compare each cell with other cells and calculate the number of matches:
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
Note

- Three nested `for` loops are sufficient to compare each cell with all other cells in the data frame and calculate the number of matches.
- The first loop `i` iterates over the columns of the data frame.
- The second loop `j` iterates over the columns of the data frame.
- The third loop `k` iterates over the rows of the data frame.
- Comparing each cell at positions `[k, i]`) and `[k, i]` for matches within the same row, comparing each cell with all other cells in the row.
- When `i == j`, the comparison is among each cell of the column with every other cell in the same column, including the diagonal cells of the matrix. For example, conisder `i == j == 1` which represents the first column, we are comparing each cell of the first column `df[k, 1]` with every other cell in the first column `df[k, 1]` where `k` represents the row number. The comparison includes the diagonal cells, e.g. `df[1, 1]`, `df[2, 2]` etc.

To further understand the loop, consider the following code
```R
for (i in 1:ncol(df)) {
    for (j in 1:ncol(df)) {
        for (k in 1:nrow(df)) {
            cat(paste0("i = ", i, ", j = ", j, ", k = ", k, "\n"))
            cat(paste0("Compare df[", k, ", ", i, "] and df[", k, ", ", j, "] \n"))
            }
    }
}

# i = 1, j = 1, k = 1
# Compare df[1, 1] and df[1, 1] 
# i = 1, j = 1, k = 2
# Compare df[2, 1] and df[2, 1] 
# i = 1, j = 1, k = 3
# Compare df[3, 1] and df[3, 1] 
# i = 1, j = 1, k = 4
# Compare df[4, 1] and df[4, 1] 
# i = 1, j = 2, k = 1
# Compare df[1, 1] and df[1, 2] 
# i = 1, j = 2, k = 2
# Compare df[2, 1] and df[2, 2] 
# i = 1, j = 2, k = 3
# Compare df[3, 1] and df[3, 2] 
# i = 1, j = 2, k = 4
# Compare df[4, 1] and df[4, 2] 
# i = 1, j = 3, k = 1
# Compare df[1, 1] and df[1, 3] 
# i = 1, j = 3, k = 2
# Compare df[2, 1] and df[2, 3] 
# i = 1, j = 3, k = 3
# Compare df[3, 1] and df[3, 3] 
# i = 1, j = 3, k = 4
# Compare df[4, 1] and df[4, 3] 
# i = 1, j = 4, k = 1
# Compare df[1, 1] and df[1, 4] 
# i = 1, j = 4, k = 2
# Compare df[2, 1] and df[2, 4] 
# i = 1, j = 4, k = 3
# Compare df[3, 1] and df[3, 4] 
# i = 1, j = 4, k = 4
# Compare df[4, 1] and df[4, 4] 
# i = 2, j = 1, k = 1
# Compare df[1, 2] and df[1, 1] 
```
... and so on.

Compare the `match_table` with `df` and the transposed dataframe `t(df)`
```R
> df
#   test_1 test_2 test_3 test_4
# 1   Pass   <NA>   Pass   Pass
# 2   <NA>   <NA>   <NA>   Pass
# 3   <NA>   Pass   Pass   <NA>
# 4   Pass   Pass   <NA>   <NA>

> t(df)
#        [,1]   [,2]   [,3]   [,4]  
# test_1 "Pass" NA     NA     "Pass"
# test_2 NA     NA     "Pass" "Pass"
# test_3 "Pass" NA     "Pass" NA    
# test_4 "Pass" "Pass" NA     NA 

> match_table
#        test_1 test_2 test_3 test_4
# test_1      2      1      1      1
# test_2      1      2      1      0
# test_3      1      1      2      1
# test_4      1      0      1      2
```
\
(4) As the upper and lower triangles are mirror images, only the upper triangle is printed
```R
> match_table[lower.tri(match_table)] <- NA
> match_table
#        test_1 test_2 test_3 test_4
# test_1      2      1      1      1
# test_2     NA      2      1      0
# test_3     NA     NA      2      1
# test_4     NA     NA     NA      2
```
\
(5) In case you need to extract values of the diagnonal cells
```R
> diag(match_table)
# test_1 test_2 test_3 test_4 
#     2      2      2      2 
```
