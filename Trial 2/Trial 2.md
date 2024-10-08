# Trial 2

## Table of Contents
- [Raw growth of shoots](#raw-growth-of-shoots)
- [Model generation & ANOVA](#model-generation-and-anova-result)
   - [Outlier removal](#outlier-removal)
- [Digital PCR results](#digital-pcr-results)   
- [R scripts](#code-to-generate)

## Raw growth of shoots

<kdb>
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/Raw%20shoot%20mass.png">
   Caption: Raw shoot growth, boxplot with mean and IQR ranges.
</kdb>


<kdb>
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/violin%20plot%20raw%20data.png">
   Caption: Raw shoot growth as violin plot, with boxplot overlayed. Single points are outliers based on IQR range (1.5*IQR in either direction).
</kdb>


#### Interpretation: 
Each salt level had a significant effect on growth - this is in contrast to trial one results which saw little difference between some salt levels. The control plant appears to have outperformed all other treatments at each salt level, a stark difference to what was seen in trial one. Differences between the 'NS' and 'S' inoculants can be seen across the experiment, with the 'S' inoculants performing better than their 'NS' counterparts. Can see some obvious outliers in the data - may be worth trimming these out if they fall outside the 1.5x IQR. 

## Model generation and ANOVA result

Replicate used as random effect (blocking). 
```
Linear mixed model fit by REML. t-tests use Satterthwaite's method ['lmerModLmerTest']
Formula: shoots ~ variety * inoculant * salt + (1 | replicate)
   Data: raw_data

REML criterion at convergence: 967

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-7.0048 -0.3303  0.0096  0.4262  3.1763 

Random effects:
 Groups    Name        Variance Std.Dev.
 replicate (Intercept) 0.01013  0.1007  
 Residual              0.19263  0.4389  
Number of obs: 755, groups:  replicate, 5
```
```
Type III Analysis of Variance Table with Satterthwaite's method
                        Sum Sq Mean Sq NumDF  DenDF   F value    Pr(>F)    
variety                   4.40    2.20     2 595.04   11.4163 1.364e-05 ***
inoculant                14.64    1.22    12 595.07    6.3323 1.290e-10 ***
salt                   1466.30  488.77     3 595.08 2537.3406 < 2.2e-16 ***
variety:inoculant         2.47    0.10    24 595.06    0.5352  0.967087    
variety:salt             10.14    1.69     6 595.06    8.7726 3.556e-09 ***
inoculant:salt           12.33    0.34    36 595.06    1.7776  0.004011 ** 
variety:inoculant:salt   10.60    0.15    72 595.06    0.7641  0.922807    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

<kdb>
    <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/QC%20of%20model.png">
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/raw%20data%2095%20con%20comb%20vars.png">
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/raw%20data%2095%20con%20with%20var.png">
</kdb>

Interpretation:

Model fit is... have seen better - while the residual distribution is centered around 0, outliers do exist (you can see that just in the raw shoot data). There is a fair amount of residual variance leftover, even accounting for variance in replicate (which is quite small overall). Suggests model could be refined further.

QC plots suggest model is ok - blocks forming along the x-axis (fitted values) is a function of shoot growth, and is expected due to the collection of data. Residual v fitted are randomly scattered, which is good. QQ plot has a lot of deviation at the ends, suggests non-normality of residuals. Scale-location is not completely horizontal, but doesn't look systematic. Maybe needs transforming - can try later.

Salt is by far the most influential factor on shoot mass, followed by variety and inoculant.
The effects of variety and salt are strong, and there is a notable interaction between variety and salt, and inoculant and salt. Variety does not apear to be affected by inoculation. The combined interaction between variety, inoculant, and salt is not significant, indicating that the three factors together don’t explain additional variability beyond their individual effects.

### Outlier removal

Reasoning: Large outliers can be seen in the raw data (especially where plants that were expected to grow), and is likely due to poor seed performance rather than experimental effects. Removing these should see an improvement in model performance.

<kdb>
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/raw%20data%20no%20outliers.png">
      Caption: Raw shoot growth, boxplot with mean and IQR ranges, original outliers removed.
</kdb>


<kdb>
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/violin%20of%20clean%20data%20no%20outliers.png">
      Caption: Raw shoot growth, outliers removed, as violin plot, with boxplot overlayed. Single points are outliers based on IQR range (1.5*IQR in either direction).
</kdb>


```
Linear mixed model fit by REML. t-tests use Satterthwaite's method [lmerModLmerTest]
Formula: shoots ~ inoculant * salt * variety + (1 | replicate)
   Data: cleaned_data

REML criterion at convergence: 675.3

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-6.8212 -0.3695 -0.0193  0.3907  3.7402 

Random effects:
 Groups    Name        Variance Std.Dev.
 replicate (Intercept) 0.007404 0.08605 
 Residual              0.133614 0.36553 
Number of obs: 687, groups:  replicate, 5
```
```
Type III Analysis of Variance Table with Satterthwaite's method
                        Sum Sq Mean Sq NumDF  DenDF   F value    Pr(>F)    
inoculant                12.63    1.05    12 527.15    7.8768 1.343e-13 ***
salt                   1384.99  461.66     3 527.22 3455.2053 < 2.2e-16 ***
variety                   4.31    2.15     2 527.13   16.1216 1.601e-07 ***
inoculant:salt            9.39    0.26    36 527.14    1.9531 0.0009713 ***
inoculant:variety         3.82    0.16    24 527.14    1.1917 0.2423543    
salt:variety              7.08    1.18     6 527.15    8.8295 3.449e-09 ***
inoculant:salt:variety   14.30    0.20    72 527.12    1.4861 0.0085356 ** 
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
```

<kdb>
   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/QC%20of%20cleaned%20model.png">

   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/estimated%20means%2095%20con%20merged%20var.png">

   <img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/estimated%20means%2095%20confidence%20sep%20var.png">
</kdb>

#### Interpretation:

Model convergence went down (that's good), tails seem to have improved. Removing these outliers also uncovered a signficant 3-way interaction between the variables, however I suspect this is simply a hangover of how strong the salt effect is on variety. Could be interesting to explore, however for now, visually, it looks like a non-effect. We'll see. 

### Digital PCR results

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/Average%20abundance%20percentages%20for%20combined%20varieties.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/boxplot%20AMF.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/boxplot%20its.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/boxplot%2016s.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/boxplot%20oomycetes.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/AMF%20to%20Bacteria.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/ITS%20to%20Bac.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/oom%20to%20amf.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/AMF%20vs%20growth%20loess.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/16S%20vs%20growth%20loess.png">

<img src="https://github.com/roylejw/barley/blob/main/Trial%202/images/ITS%20vs%20growth%20loess.png">




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

# Violin plot
plots <- lapply(1:4, function(i) {
    ggplot(datasets[[i]], aes(x = inoculant, y = shoots, fill = inoculant)) +
        geom_violin(trim = FALSE, alpha = 0.7) +  # Add violin plot
        geom_boxplot(width = 0.2, outlier.shape = NA, color = "black") +  # Add boxplot without outliers
        stat_boxplot(geom = "errorbar", width = 0.1) +  # Add error bars for the boxplot
        geom_jitter(data = datasets[[i]][datasets[[i]]$shoots < (quantile(datasets[[i]]$shoots, 0.25) - 1.5 * IQR(datasets[[i]]$shoots)) | 
                                             datasets[[i]]$shoots > (quantile(datasets[[i]]$shoots, 0.75) + 1.5 * IQR(datasets[[i]]$shoots)), ],
                    width = 0.2, alpha = 0.5, size = 1.5, shape = 21, fill = "black") +  # Add only outlier points
        labs(x = "Inoculant", y = "Shoot mass (g)", fill = "Salt level", title = titles[i]) +
        theme(plot.title = element_text(hjust = 0.5), axis.text.x = element_text(size = 6))  # Center title
})
combined_plot <- wrap_plots(plots, ncol = 2)
combined_plot
```
### Model and QC plots

```
shoot_model <- lmer(shoots ~ variety * inoculant * salt + (1 | replicate), raw_data)
anova <- anova(shoot_model)
print(anova)

# Residuals vs Fitted Plot
residuals_vs_fitted <- ggplot(data = data.frame(Fitted = fitted(model), Residuals = resid(model)), 
                              aes(x = Fitted, y = Residuals)) +
    geom_point(alpha = 0.5) +
    geom_hline(yintercept = 0, linetype = "dashed", color = "red") +
    labs(title = "Residuals vs Fitted", x = "Fitted values", y = "Residuals") +
    theme_minimal()

# QQ Plot for Residuals
qq_plot <- ggplot(data = data.frame(Residuals = resid(model)), aes(sample = Residuals)) +
    stat_qq() +
    stat_qq_line(color = "red") +
    labs(title = "QQ Plot of Residuals") +
    theme_minimal()

# Scale-Location Plot (sqrt of standardized residuals)
scale_location <- ggplot(data = data.frame(Fitted = fitted(model), 
                                           StdRes = sqrt(abs(resid(model))) / sd(resid(model))), 
                         aes(x = Fitted, y = StdRes)) +
    geom_point(alpha = 0.5) +
    geom_smooth(se = FALSE, color = "blue") +
    labs(title = "Scale-Location Plot", x = "Fitted values", y = "Square Root of Standardized Residuals") +
    theme_minimal()


# Combine all plots
grid.arrange(residuals_vs_fitted, qq_plot, scale_location, ncol = 2)
```
### Outlier removal

```
remove_outliers <- function(df) {
    df %>%
        group_by(variety, inoculant, salt) %>%
        mutate(
            Q1 = quantile(shoots, 0.25),
            Q3 = quantile(shoots, 0.75),
            IQR = Q3 - Q1,
            lower_bound = Q1 - 1.5 * IQR,
            upper_bound = Q3 + 1.5 * IQR
        ) %>%
        filter(shoots >= lower_bound & shoots <= upper_bound) %>%
        ungroup()  # Ungrouping to avoid issues in further operations
}
cleaned_data <- remove_outliers(raw_data)
cleaned_model <- lmer(shoots ~ inoculant * salt * variety + (1 | replicate), cleaned_data)
clean_anova <- anova(cleaned_model)
emm_clean_data <- emmeans(cleaned_model, ~ inoculant * salt * variety)
clean_df <- as.data.frame(emm_clean_data)
clean_df$inoculant <- factor(clean_df$inoculant,
                                levels = c("Control", setdiff(unique(clean_df$inoculant), "Control")))
clean_df$salt <- factor(clean_df$salt, levels = c("0 dS/m", "6 dS/m", "12 dS/m", "18 dS/m"))

plot_list <- list()
salt_levels <- levels(clean_df$salt)
for (salt in salt_levels) {
    p <- ggplot(clean_df[clean_df$salt == salt, ], aes(x = inoculant, y = emmean, ymin = lower.CL, ymax = upper.CL, color = variety)) +
        geom_point(position = position_dodge(width = 0.5)) +
        geom_errorbar(position = position_dodge(width = 0.5), width = 0.2) +
        labs(y = "Shoot Mass", x = "inoculant", title = NULL) +
        ggtitle(paste("Salt Level:", salt)) +
        theme(plot.title.position = "plot", plot.title = element_text(hjust = 1))
    
    plot_list[[salt]] <- p
}

combined_plot <- wrap_plots(plot_list, ncol = 1) + plot_annotation(title = "Estimated means with 95% confidence intervals", theme = theme(plot.title = element_text(hjust = 0.5)))
print(combined_plot)

### Repeat for emm calculation with no variety factor ###

```
### Digital PCR results

Data was pre-filtered to only include samples that had dPCR results. This new csv was imported.
[CSV can be found here.](https://github.com/roylejw/barley/blob/main/Trial%202/data/trial_2_dpcr_included.csv)

```
data_filtered <- read.csv("trial_2_dpcr_included.csv")

### boxplot for Fungal-bacterial ratios. Replace amfb with other headers to generate other graphs
ggplot(data_filtered, aes(x = inoculant, y = amfb, fill = salt)) +
    geom_boxplot(position = position_dodge(width = 0.7), width = 0.6) +
    labs(x = "Inoculant", y = "F:B ratio", fill = "Salt level", title = "Fungal to Bacteria ratio across treatments and salt levels") +
    scale_y_continuous(breaks = seq(0, max(data_filtered$FB), by = 0.1)) +
    theme(plot.title = element_text(hjust = 0.5))  # Centers the title

### Contrasts of growth v AMF per inoculant, replace y axis as needed

ggplot(data_filtered, aes(x = shoots, y = log_amf, color = salt)) +
    geom_point() +
    geom_smooth(method = "loess", span = 1, aes(group = inoculant)) +
    facet_wrap(~ inoculant) +
    labs(title = "Contrast of total AMF against Plant Growth across AMF Treatments and Salinity Levels")

