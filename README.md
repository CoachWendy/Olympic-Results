
## Olympic Data



![Age at First Olympics]([Age at First Olympics.png](https://github.com/CoachWendy/Olympic-Results/blob/main/Age%20at%20First%20Olympics.png).)






Installing all the necessary packages

```{r}
install.packages("tidyverse")
library(tidyverse)
install.packages("readr")
library("readr")
install.packages("here")
library("here")
install.packages("skimr")
library("skimr")
install.packages("janitor")
library("janitor")
install.packages("dyplyr")
library("dplyr")
```

Filter data in Olympic_results data set to just display the various forms of gymnastics

```{r}
filtered_olympic_results <- olympic_results[grepl("gymnastics", olympic_results$discipline_title, ignore.case = TRUE), ]
```

create a new column that displays the first year they competed in the Olympics

```{r}
olympic_athletes$first_olympic_year <- substr(olympic_athletes$first_game, nchar(olympic_athletes$first_game) - 3, nchar(olympic_athletes$first_game))
```

Convert all year and age related columns to integers

```{r}
olympic_athletes$first_olympic_year <- as.integer(olympic_athletes$first_olympic_year)
olympic_athletes$athlete_year_birth <- as.integer(olympic_athletes$athlete_year_birth)
olympic_athletes$first_olympic_year <- as.integer(olympic_athletes$first_olympic_year)
```

Subtract the First Olympic Year from the Athletes Birth Year to get their age at competition

```{r}
olympic_athletes$age_at_competition <- olympic_athletes$first_olympic_year - olympic_athletes$athlete_year_birth
```

filtered data in olympic_medals to display various forms of gymnastics

```{r}
filtered_olympic_medals <- subset(olympic_medals, grepl("gymnastics", discipline_title, ignore.case = TRUE))
```

Merge the data sets based on the "athlete_full_name" field

```{r}
merged_data <- merge(filtered_olympic_results, olympic_athletes, by.x = "athlete_full_name", by.y = "athlete_full_name")
```

Realized I needed to get a little better at naming things so I switched some stuff up

```{r}
gymnastic_medals <- filtered_olympic_medals
gymnastic_results <- filtered_olympic_results
gymnastic_data <- merged_data
rm(filtered_olympic_medals)
rm(filtered_olympic_results)
rm(merged_data)
```

Created a column that shows if the athlete was male or female based on the event they participated in

```{r}
gymnastic_data$male_female <- ifelse(grepl("women", gymnastic_data$event_title, ignore.case = TRUE), "female", "male")
```

in need to install dplyr for filter goodness

```{r}
install.packages("dplyr")
library("dplyr")
```

needed to change age_at_competition to an int

```{r}
gymnastic_data$age_at_competition <- as.integer(gymnastic_data$age_at_competition)
```

creating a copy of the gymnastic_data data because I keep messing up my filters.

```{r}
gymnastic_data_copy <- gymnastic_data
```

changed the name of the column age_at_competition to age for ease of typing

```{r}
 names(gymnastic_data)[names(gymnastic_data) == "age_at_competition"] <- "age"
```

filtering out all data where age is less than 10 or over 45

```{r}
gymnastic_data <- gymnastic_data[gymnastic_data$age >= 10, ]
gymnastic_data <- gymnastic_data[gymnastic_data$age <= 45, ]
```

changing the athlete name to all lower case then to proper case because for some reason it wouldn't do it with just proper case first

```{r}
gymnastic_data$athlete_full_name <- tolower(gymnastic_data$athlete_full_name)
gymnastic_data$athlete_full_name <- toTitleCase(gymnastic_data$athlete_full_name)

```

noticed there were some rows without any data... those must go

```{r}
gymnastic_data <- gymnastic_data[!is.na(gymnastic_data$athlete_full_name), ]
```

the column bio was giving me fits... must remove

```{r}
gymnastic_data$bio <- NULL
```

Counting up the medals

```{r}
gymnastic_data <- gymnastic_data %>%
 mutate(gold = str_count(medal_type, "GOLD"))

gymnastic_data <- gymnastic_data %>%
 mutate(silver = str_count(medal_type, "SILVER"))

gymnastic_data <- gymnastic_data %>%
 mutate(bronze = str_count(medal_type, "BRONZE"))
```

Total Medals

saving file locally so I can work in tableau to create an awesome chart.

```{r}
write.csv(gymnastic_data, "gymnastic_data.csv")
```
