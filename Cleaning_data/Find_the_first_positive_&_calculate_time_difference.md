Loading required package `dplyr`

```R
library(dplyr)
```

Generate sample data frame

```R
df <- data.frame(
  reference = c(1, 1, 1, 2, 2, 3, 3, 3),
  value = c("positive 1", "positive 2", "negative", "positive 1", "positive 2", "negative", "negative", "negative"),
  datetime = c("2023-01-01 10:00:00", "2023-01-01 11:00:00", "2023-01-01 12:00:00",
               "2023-01-01 13:00:00", "2023-01-01 14:00:00", "2023-01-01 15:00:00",
               "2023-01-01 16:00:00", "2023-01-01 17:00:00")
)
```

View the dataframe

```R
> df
#   reference      value            datetime
# 1         1 positive 1 2023-01-01 10:00:00
# 2         1 positive 2 2023-01-01 11:00:00
# 3         1   negative 2023-01-01 12:00:00
# 4         2 positive 1 2023-01-01 13:00:00
# 5         2 positive 2 2023-01-01 14:00:00
# 6         3   negative 2023-01-01 15:00:00
# 7         3   negative 2023-01-01 16:00:00
# 8         3   negative 2023-01-01 17:00:00
```

Identify first occurrence of value change from positive to negative

```R
output_df <- df %>%
  group_by(reference) %>%
  mutate(group = cumsum((value == "positive 1" | value == "positive 2") & lead(value) == "negative")) %>%
  filter(group > 0) %>%
  group_by(reference, group) %>%
  summarise(value_start = first(value),
            value_end = last(value),
            time_diff = round(difftime(last(datetime), first(datetime), units = "hours"), digits = 2),
            first_time = first(datetime),
            last_time = last(datetime),
            .groups = "drop")
```

Print the output data frame
```R
print(output_df)

#  A tibble: 1 × 7
#   reference group value_start value_end first_time          last_time           time_diff
#       <dbl> <int> <chr>       <chr>     <chr>               <chr>               <drtn>   
# 1         1     1 positive 2  negative  2023-01-01 11:00:00 2023-01-01 12:00:00 1 hours 
```
