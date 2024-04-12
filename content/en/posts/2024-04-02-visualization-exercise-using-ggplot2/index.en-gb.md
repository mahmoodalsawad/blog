---
title: Visualization exercise using ggplot2
author: Mahmood Alsawad
date: '2024-04-02'
slug: []
categories: []
tags: []
description: ~
---



This is an exercise on data visualization using ggplot2 in RStudio. Data has been imported from two sources:

-   [The Cooperative Congressional Election Study (CCES)](https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/WOT7O8), a major academic survey about American politics.

-   [The Center for Effective Lawmaking (CEL)](https://thelawmakers.org/data-download), a research project spearheaded by Alan Wiseman (Vanderbilt University) and Craig Volden (University of Virginia).

#### Disclaimer

The data visualizations presented in this post are for **illustrative purposes** only. The underlying data sets were sourced from aforementioned publicly available repositories and have been modified for visual clarity and efficiency. These visualizations should not be interpreted as factual representations, recommendations, or definitive findings. The sole purpose of this post is to showcase data visualization techniques and tools.

## Visualization 1

Visualizing the relationship between income and marital status across different regions with a scatter plot chart.

<img src="/en/posts/2024-04-02-visualization-exercise-using-ggplot2/index.en-gb_files/figure-html/exercise1-1.png" width="672" />

## Visualization 2

Visualizing income distribution across four regions using a histogram chart. Employing the `face_wrap()` function to create a separate histogram for each region, facilitating a clear comparison of income distribution patterns between them.

<img src="/en/posts/2024-04-02-visualization-exercise-using-ggplot2/index.en-gb_files/figure-html/exercise2-1.png" width="672" />

## Visualization 3

This visualization explores how employment status varies across different regions, broken down by gender using the `facet_wrap()` from ggplot2. To enhance readability and aesthetics, `theme_solarized()` from the ggthemes package has been applied, creating a clear and informative exploration of potential disparities in employment opportunities across geographic locations.

<img src="/en/posts/2024-04-02-visualization-exercise-using-ggplot2/index.en-gb_files/figure-html/exercise3-1.png" width="672" />

## Visualization 4

This visualization tracks the rise in female congressional representation over time, spanning the years 1973 to 2017. A line chart would be most suitable, plotting the number of female members on the y-axis and years along the x-axis, effectively depicting the trend of increasing female involvement in the U.S. Congress.

<img src="/en/posts/2024-04-02-visualization-exercise-using-ggplot2/index.en-gb_files/figure-html/exercise4-1.png" width="672" />
