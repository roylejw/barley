# Trial 2

## Raw growth of shoots

<kdb>
<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/Raw%20shoot%20mass.png">
</kdb>









## Code to generate
### Raw growth of shoots
[CSV can be found here.](https://github.com/roylejw/barley/blob/main/Trial%202/data/trial_2_ranked.csv)

```
library(dplyr)
library(ggplot2)
library(patchwork)

# Read CSV
raw_data <- read.csv("trial_2_ranked.csv")

# Mutate Salt levels to be meaningful
raw_data <- raw_data %>%
    mutate(salt = recode(salt, 
                         'S1' = '0 dS/m', 
                         'S3' = '6 dS/m', 
                         'S4' = '12 dS/m'
                         'S5' = '18 dS/m'))

# Filter datasets into salt measurements
raw_data_S1 <- raw_data %>% filter(salt == '0 dS/m')
raw_data_S2 <- raw_data %>% filter(salt == '6 dS/m')
raw_data_S3 <- raw_data %>% filter(salt == '12 dS/m')
raw_data_S4 <- raw_data %>% filter(salt == '18 dS/m')

# Convert inoculants into factors with Control first - ensures Control is first on graph at all times
raw_data_S1$inoculant <- factor(raw_data_S1$inoculant,
                                levels = c("Control", sort(unique(raw_data_S1$inoculant[raw_data_S1$inoculant != "Control"]))))
raw_data_S2$inoculant <- factor(raw_data_S2$inoculant,
                                levels = c("Control", sort(unique(raw_data_S2$inoculant[raw_data_S2$inoculant != "Control"]))))
raw_data_S3$inoculant <- factor(raw_data_S3$inoculant,
                                levels = c("Control", sort(unique(raw_data_S3$inoculant[raw_data_S3$inoculant != "Control"]))))
raw_data_S4$inoculant <- factor(raw_data_S4$inoculant,
                                levels = c("Control", sort(unique(raw_data_S4$inoculant[raw_data_S4$inoculant != "Control"]))))

# List the datasets
datasets <- list(raw_data_S1, raw_data_S2, raw_data_S3, raw_data_S4)
titles <- c("Raw shoot mass, 0 dS/m", "Raw shoot mass, 6 dS/m", "Raw shoot mass, 12 dS/m", "Raw shoot mass, 18 dS/m")

# Create a list of plots
plots <- lapply(1:4, function(i) {
    ggplot(datasets[[i]], aes(x = inoculant, y = shoots, fill = salt)) +
        geom_boxplot(position = position_dodge(width = 0.7), width = 0.6) +
        labs(x = "Inoculant", y = "Shoot mass (g)", fill = "Salt level", title = titles[i]) +
        theme(plot.title = element_text(hjust = 0.5), axis.text.x = element_text(size = 6))  # Centers the title
})

# Combine the plots into one image
combined_plot <- wrap_plots(plots, ncol = 2)
combined_plot
```
