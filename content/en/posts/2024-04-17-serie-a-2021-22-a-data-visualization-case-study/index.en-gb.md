---
title: 'Serie A 2021-22: A Data Visualization Case Study'
author: Mahmood Alsawad
date: '2024-04-17'
slug: []
categories: []
tags: []
description: ~
---

<link href="/rmarkdown-libs/htmltools-fill/fill.css" rel="stylesheet" />
<script src="/rmarkdown-libs/htmlwidgets/htmlwidgets.js"></script>
<script src="/rmarkdown-libs/plotly-binding/plotly.js"></script>
<script src="/rmarkdown-libs/typedarray/typedarray.min.js"></script>
<script src="/rmarkdown-libs/jquery/jquery.min.js"></script>
<link href="/rmarkdown-libs/crosstalk/css/crosstalk.min.css" rel="stylesheet" />
<script src="/rmarkdown-libs/crosstalk/js/crosstalk.min.js"></script>
<link href="/rmarkdown-libs/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />
<script src="/rmarkdown-libs/plotly-main/plotly-latest.min.js"></script>

### Introduction

Serie A, Italy’s top football league, has captivated audiences for over a century. The 2021-2022 season, which saw AC Milan reclaim the championship title, was no exception. This report delves into that season’s data, wielding ggplot2, a powerful R package for data visualization, to unlock the stories embedded within the statistics. My aim, with this report isn’t to bombard you with facts and figures, but rather to showcase the transformative potential of data visualization. Through captivating graphs and charts, I hope to illuminate trends, highlight team performance, and ultimately, paint a vivid picture of the 2021-2022 Serie A season.

#### Data Sources

This report relies on data collected from the following sources::

- [Football-Data.co.uk](https://www.football-data.co.uk)
- [FBref.com](https://fbref.com/en/comps/11/Serie-A-Stats) via [Mustafa Gurkan’s Kaggle dataset](https://www.kaggle.com/datasets/mechatronixs/20212022-season-italy-seriea-team-datasets)

#### Disclaimer

The data visualizations presented in this post are for **illustrative purposes** only. The underlying data sets were sourced from aforementioned publicly available repositories and have been modified for visual clarity and efficiency. These visualizations should not be interpreted as factual representations, recommendations, or definitive findings. The sole purpose of this post is to showcase data visualization techniques and tools.

### Visualization 1 - Home Field Advantage

This section dives into the number of wins each team achieved while playing at their home stadium. As shown in the plot, Inter takes the lead with the most home victories (14), showcasing a dominant performance on their home turf. Following closely behind is Fiorentina, proving to be a strong contender at home. On the other hand, Cagliari, Genoa, Salernitana and Venezia seem to struggle more on their home field with only 3 wins, grabbing the fewest wins amongst all the teams.

``` r
viz1 <- data %>% 
  group_by(HomeTeam) %>% 
  summarize("Home_Win" = sum(FTR == "H"))

ggplot(viz1, aes(y = reorder(HomeTeam, Home_Win), x = Home_Win)) +
  geom_point(size = 3) +
  geom_segment(aes(y = HomeTeam, yend = HomeTeam, x = 0, xend = Home_Win)) +
  labs(y = NULL, x = "Games Won at Home",
       title = "Inter is the team with the most wins at home",
       caption = "Source: football-data.co.uk") +
  theme_minimal()
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualizaiton_2-1.png" width="672" />

### Visualization 2 - Comeback Kings: Teams Clawing Back from First-Half Deficits

This chart highlights the number of comeback victories for teams that trailed at halftime. It’s important to remember that only teams with at least one comeback win are showcased here. Teams that haven’t secured any come-from-behind wins this season are excluded from this view.

Among the comeback victors, AC Milan reigns supreme with a remarkable 3 comeback wins this season!

**Key Points to Consider**

- This visualization solely focuses on situations where a team was trailing at halftime, then rallied to win the game.
- Ties in the first half aren’t included, and neither are situations where a team trailed in the first half but only managed a draw in the final score.

``` r
viz2 <- data %>% 
  select(HomeTeam, AwayTeam, HTR, FTR) %>% 
  filter(HTR != FTR & HTR != "D" & FTR %in% c("H","A")) %>% 
  mutate(comeback_winner = ifelse(FTR == "H", HomeTeam, AwayTeam)) %>% 
  group_by(comeback_winner) %>% 
  summarise(comeback_wins = n()) %>% 
  arrange(desc(comeback_wins))

ggplot(viz2, aes(y = fct_reorder(comeback_winner, comeback_wins), 
                 x = comeback_wins)) + 
  geom_col(fill = "lightgrey") +
  theme_minimal() +
  labs(y = NULL, x = "Number of comback wins",
       title = "Milan, Inter, and Empoli are the teams with the most comeback wins",
       caption = "Source: football-data.co.uk") +
  guides(fill = "none")
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualization_3-1.png" width="672" />

### Vizualization 3 - Squad Age Distribution: 2021-22 Season

This section dives into the average age of Serie A squads for the 2021-22 season. The data, visualized in a table below (generated using the `kable` function from the `knitr` package), reveals interesting trends regarding team experience.

We can see that Lazio and Sampdoria boasted the league’s most seasoned squads, with an average player age of 29 years old. On the other end of the spectrum, Spezia fielded the youngest team, with an average age of just 24. Interestingly, AC Milan, the eventual champions, possessed the fourth youngest squad, averaging 25.6 years old.

``` r
table_age <- data2 %>% 
  group_by(Squad) %>% 
  select(Age) %>% 
  arrange(desc(Age))

kable(table_age, results = 'asis')
```

| Squad         |  Age |
|:--------------|-----:|
| Lazio         | 29.0 |
| Sampdoria     | 29.0 |
| Inter         | 28.9 |
| Napoli        | 27.9 |
| Salernitana   | 27.7 |
| Atalanta      | 27.5 |
| Juventus      | 27.4 |
| Genoa         | 27.0 |
| Hellas Verona | 26.8 |
| Bologna       | 26.6 |
| Cagliari      | 26.5 |
| Fiorentina    | 26.4 |
| Roma          | 26.4 |
| Udinese       | 26.3 |
| Sassuolo      | 26.1 |
| Venezia       | 26.1 |
| Milan         | 25.6 |
| Torino        | 25.2 |
| Empoli        | 24.5 |
| Spezia        | 24.4 |

To complement the table above and provide a visually compelling exploration of the data, a lollipop-style bar chart is presented next. This chart offers a clear and concise representation of the average squad age distribution across all Serie A teams during the 2021-22 season. By utilizing a bar chart format, comparisons between teams become readily apparent. Additionally, the lollipop chart incorporates data points that pinpoint the specific average age for each team, allowing for a more nuanced understanding of the data. This visual representation will further enhance your ability to identify trends and patterns within the age composition of Serie A squads.

``` r
ggplot(data2, aes(y = fct_reorder(Squad, Age), x = Age)) +
  geom_point() +
  geom_segment(aes(y = Squad, yend = Squad, x = 20, xend = Age))+
  labs(x = "Average Squad Age",
       y = NULL,
       title = "Lazio and Sampdoria have the highest average squad age",
       subtitle = "Spezia with the yougnest squad",
       caption = "Source: fbref.com/en/comps/11/Serie-A-Stats"
       ) +
  theme_minimal()
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualization_4-1.png" width="672" />

### Viualization 4 - Examining Match Results by Time: A Look with ggplot2

This section utilizes a scatter plot, generated using the `ggplot2` package in RStudio, to explore the relationship between match day time and the outcome (win, loss, or draw sorted by home/away team). Each data point represents a single match, with its position on the x-axis indicating the time of day and the y-axis signifying the win/loss/draw result.

While the scatter plot allows us to visually assess potential trends, it appears there’s no clear clustering or pattern in the data. This suggests that the time of day a match is played has little to no bearing on the final result. In other words, the graph indicates no significant correlation between these variables.

It’s important to acknowledge that this scatter plot might not be the most informative visualization for this particular dataset. The variables themselves (match time and win/loss/draw) might not provide the most insightful analysis. However, the purpose here is to showcase the capabilities of `ggplot2` and its interactive counterpart `ggplotly` from the `plotly` package in RStudio for data visualization.

**Interactive Insights:** Thanks to the power of `ggplotly`, you can hover your mouse over any data point (dot) on the scatter plot to reveal additional information about that specific match. This interactivity can provide a more nuanced understanding of the data beyond the overall trends.

``` r
viz1 <- data %>% 
  select(Date,Time, FTR) %>% 
  group_by(Time) %>% 
  summarize("Home Team Win" = sum(FTR == "H"),
            "Away Team Win" = sum(FTR == "A"),
            "Draw" = sum(FTR == "D"),
            )

viz11 <-  pivot_longer(viz1, cols = 2:4,
                       names_to = "Result",
                       values_to = "Count")

ggplotly(ggplot(viz11, aes(x = Time, y = Count, color = Result)) + 
           geom_point() + 
           theme_minimal() +
           scale_color_brewer(palette = "Set1") +
           labs(title = "Time of match does not have any influence on the result",
                caption = "Source: football-data.co.uk")
)
```

<div class="plotly html-widget html-fill-item" id="htmlwidget-1" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"data":[{"x":[41400,48600,50400,55800,57600,58500,61200,63000,63900,64800,69300,71100,72000],"y":[13,2,29,3,0,1,22,11,1,1,0,49,2],"text":["Time: 11:30:00<br />Count: 13<br />Result: Away Team Win","Time: 13:30:00<br />Count:  2<br />Result: Away Team Win","Time: 14:00:00<br />Count: 29<br />Result: Away Team Win","Time: 15:30:00<br />Count:  3<br />Result: Away Team Win","Time: 16:00:00<br />Count:  0<br />Result: Away Team Win","Time: 16:15:00<br />Count:  1<br />Result: Away Team Win","Time: 17:00:00<br />Count: 22<br />Result: Away Team Win","Time: 17:30:00<br />Count: 11<br />Result: Away Team Win","Time: 17:45:00<br />Count:  1<br />Result: Away Team Win","Time: 18:00:00<br />Count:  1<br />Result: Away Team Win","Time: 19:15:00<br />Count:  0<br />Result: Away Team Win","Time: 19:45:00<br />Count: 49<br />Result: Away Team Win","Time: 20:00:00<br />Count:  2<br />Result: Away Team Win"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(228,26,28,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(228,26,28,1)"}},"hoveron":"points","name":"Away Team Win","legendgroup":"Away Team Win","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[41400,48600,50400,55800,57600,58500,61200,63000,63900,64800,69300,71100,72000],"y":[7,1,23,1,0,0,14,13,1,2,1,33,2],"text":["Time: 11:30:00<br />Count:  7<br />Result: Draw","Time: 13:30:00<br />Count:  1<br />Result: Draw","Time: 14:00:00<br />Count: 23<br />Result: Draw","Time: 15:30:00<br />Count:  1<br />Result: Draw","Time: 16:00:00<br />Count:  0<br />Result: Draw","Time: 16:15:00<br />Count:  0<br />Result: Draw","Time: 17:00:00<br />Count: 14<br />Result: Draw","Time: 17:30:00<br />Count: 13<br />Result: Draw","Time: 17:45:00<br />Count:  1<br />Result: Draw","Time: 18:00:00<br />Count:  2<br />Result: Draw","Time: 19:15:00<br />Count:  1<br />Result: Draw","Time: 19:45:00<br />Count: 33<br />Result: Draw","Time: 20:00:00<br />Count:  2<br />Result: Draw"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(55,126,184,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(55,126,184,1)"}},"hoveron":"points","name":"Draw","legendgroup":"Draw","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"x":[41400,48600,50400,55800,57600,58500,61200,63000,63900,64800,69300,71100,72000],"y":[13,1,36,2,1,0,31,16,2,0,1,41,4],"text":["Time: 11:30:00<br />Count: 13<br />Result: Home Team Win","Time: 13:30:00<br />Count:  1<br />Result: Home Team Win","Time: 14:00:00<br />Count: 36<br />Result: Home Team Win","Time: 15:30:00<br />Count:  2<br />Result: Home Team Win","Time: 16:00:00<br />Count:  1<br />Result: Home Team Win","Time: 16:15:00<br />Count:  0<br />Result: Home Team Win","Time: 17:00:00<br />Count: 31<br />Result: Home Team Win","Time: 17:30:00<br />Count: 16<br />Result: Home Team Win","Time: 17:45:00<br />Count:  2<br />Result: Home Team Win","Time: 18:00:00<br />Count:  0<br />Result: Home Team Win","Time: 19:15:00<br />Count:  1<br />Result: Home Team Win","Time: 19:45:00<br />Count: 41<br />Result: Home Team Win","Time: 20:00:00<br />Count:  4<br />Result: Home Team Win"],"type":"scatter","mode":"markers","marker":{"autocolorscale":false,"color":"rgba(77,175,74,1)","opacity":1,"size":5.6692913385826778,"symbol":"circle","line":{"width":1.8897637795275593,"color":"rgba(77,175,74,1)"}},"hoveron":"points","name":"Home Team Win","legendgroup":"Home Team Win","showlegend":true,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":43.762557077625573,"r":7.3059360730593621,"b":40.182648401826498,"l":37.260273972602747},"font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724},"title":{"text":"Time of match does not have any influence on the result","font":{"color":"rgba(0,0,0,1)","family":"","size":17.534246575342465},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[39870,73530],"tickmode":"array","ticktext":["12:00:00","14:00:00","16:00:00","18:00:00","20:00:00"],"tickvals":[43200,50400,57600,64800,72000],"categoryorder":"array","categoryarray":["12:00:00","14:00:00","16:00:00","18:00:00","20:00:00"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"y","title":{"text":"Time","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"yaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[-2.4500000000000002,51.450000000000003],"tickmode":"array","ticktext":["0","10","20","30","40","50"],"tickvals":[0,10,20,30.000000000000004,40,50],"categoryorder":"array","categoryarray":["0","10","20","30","40","50"],"nticks":null,"ticks":"","tickcolor":null,"ticklen":3.6529680365296811,"tickwidth":0,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.68949771689498},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(235,235,235,1)","gridwidth":0.66417600664176002,"zeroline":false,"anchor":"x","title":{"text":"Count","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}},"hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":1}],"showlegend":true,"legend":{"bgcolor":null,"bordercolor":null,"borderwidth":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.68949771689498},"title":{"text":"Result","font":{"color":"rgba(0,0,0,1)","family":"","size":14.611872146118724}}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"source":"A","attrs":{"1b133ddb84f3":{"x":{},"y":{},"colour":{},"type":"scatter"}},"cur_data":"1b133ddb84f3","visdat":{"1b133ddb84f3":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

### Viualization 5 - Ball Possession Breakdown: A Bar Chart Analysis

The following bar chart provides a clear illustration of average ball possession percentages across the league, offering a key metric for evaluating team strategies. Napoli stands out as the possession kingpin, averaging an impressive figure at around 60%. This translates to a team that thrives on controlling the flow of the game, likely employing an attacking style that prioritizes ball retention. Conversely, Salernitana resides at the opposite end of the spectrum, averaging a mere 41% possession. This suggests a more defensive approach, where conceding possession and strategically countering attacks could be their weapon of choice. The remaining teams occupy positions between these two extremes, highlighting the diverse tactical philosophies and the varying strengths each team possesses when it comes to controlling the ball. By delving into this distribution, we can glean valuable insights into the playing styles of each team and their preferred tactical approaches on the pitch.

``` r
ggplot(data2, aes(y = as.factor(fct_reorder(Squad, Poss)), x = Poss))+
  geom_bar(stat = "identity", fill = "lightgrey") +
  theme_minimal() +
  labs(y = NULL, x = "Average ball possession",
       title = "Napoli with the highest average ball possession with 58.5%",
       subtitle = "Salernitana with the lowest average ball possession at 41%",
       caption = "Source: football-data.co.uk")
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualization_5-1.png" width="672" />

### Visualization 6 - Unveiling Spatial Dominance: A Lollipop Chart Analysis of Ball Touches

This section delves into a lollipop chart visualization that offers a nuanced perspective on Serie A teams’ ball distribution across the three critical zones of the pitch: the defensive third (proximity to their own goal), the midfield third, and the attacking third (proximity to the opponent’s goal). Each data point on the chart represents a team, with the lollipop stem acting as a quantitative measure of the team’s touches within a specific zone. The lollipop head then takes center stage, highlighting the team’s total touches across all zones.

By meticulously examining the lollipop chart, we can glean valuable insights into each team’s strategic approach. Notably, Sassuolo emerges as the team exhibiting the most defensive focus, leading the league in touches within their own defensive third. This suggests a potentially more risk-averse strategy, prioritizing the safeguarding of their goal. Conversely, Salernitana appears to cede possession in this area, potentially indicative of a more aggressive or high-pressing style of play.

Shifting focus to the midfield zone, the chart reveals Lazio as the team dictating the tempo. Their high number of touches in this area suggests a strong emphasis on controlling the central zone of the pitch, potentially allowing them to exert influence over the flow of the game. Conversely, Venezia seems to struggle for control in midfield, which could translate into difficulties in building attacks or transitioning from defense to offense.

Finally, the attacking third showcases Atalanta’s dominance. Their high number of touches signifies a clear focus on occupying threatening positions and generating goal-scoring opportunities. In stark contrast, Venezia appears less threatening in the attacking zone, registering the fewest touches. This could indicate a more reserved approach in attack or difficulty in breaching opposing defenses.

The lollipop chart serves as a powerful tool for visually comprehending team positioning strategies across the pitch. By analyzing how teams distribute their touches, we can gain valuable insights into their overall approach to the game, from prioritizing defense to dictating play in midfield to creating opportunities in the attacking third.

``` r
data3 <- pivot_longer(data3, cols = 3:5,
                      names_to = "area_pitch", 
                      values_to = "touches")

data3$area_pitch <- recode(data3$area_pitch, `Def3rdTouc` = "Defensive 3rd Touches",
                           `Mid3rdTouc` = "Midfield 3rd Touches",
                           `Att3rdTouc` = "Attacking 3rd Touches")

data3$area_pitch <- factor(data3$area_pitch, levels = c("Defensive 3rd Touches", "Midfield 3rd Touches", "Attacking 3rd Touches"))

ggplot(data3, aes(y = fct_reorder(Squad,touches), x = touches))+
  geom_point() +
  geom_segment(aes(y=Squad, yend=Squad, x=4000, xend = touches))+
  facet_wrap(~area_pitch) +
  theme_bw() +
  theme(axis.text.x = element_text(angle = 90)) +
  labs(shape = "Area",
       caption = "Source: football-data.co.uk", x = "Number of touches", y = NULL)
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualization_7-1.png" width="672" />

### Visualization 7 - Another Way to Visualize Ball Touches: A Scatterplot Matrix

This scatterplot matrix offers yet another perspective on the intricate dance of ball distribution across Serie A teams. It serves as a complementary visualization to the previously explored lollipop chart.

While the lollipop chart excelled at revealing league-wide trends and team-specific emphasis on certain zones, the scatterplot matrix delves deeper, enabling direct comparisons between all teams. By employing the `facet_wrap()` function in `ggplot2`, this visualization technique constructs a grid where each cell depicts a two-dimensional scatterplot.

For instance, we can readily observe how Sassuolo, previously identified as a defensively oriented team based on the lollipop chart, compares to other teams in this regard. The scatterplot matrix might reveal if their defensive emphasis is a common strategy or a distinct outlier.

Furthermore, the beauty of this visualization lies in its ability to unearth patterns across multiple teams. By systematically examining the matrix, we can potentially uncover clusters of teams exhibiting similar ball touch distribution tendencies. These clusters could reflect shared tactical philosophies or common playing styles employed within Serie A. Conversely, outliers – teams that deviate significantly from the norm – can also be readily identified. These outliers might hint at unique strategic approaches or adaptations employed to counter specific opponents.

In essence, the scatterplot matrix complements the lollipop chart beautifully. While the lollipop chart provided a high-level overview, the scatterplot matrix delves into the intricate details, enabling a more nuanced understanding of how each team allocates its touches across the pitch and how these strategies compare to their competitors. This multifaceted approach equips us to form a well-rounded picture of team ball distribution patterns within Serie A.

``` r
ggplot(data3, aes(x = area_pitch, y = touches, shape = area_pitch))+
  geom_point() +
  theme(axis.text.x = element_blank())+
  facet_wrap(~Squad) +
  labs(shape = "Area",title = "Atalanta with the highest number of touches in the openents' third",
       caption = "Source: football-data.co.uk", y = "Number of touches", x = NULL)+
  theme(legend.position = "bottom")
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualization_8-1.png" width="672" />

### Visualization 8 - Unveiling Disciplinary Trends: A Look at Red and Yellow Cards

This section utilizes a bar chart to provide a comprehensive breakdown of disciplinary actions in Serie A for the recently concluded season. The data reveals Venezia as the team with the highest number of dismissals (red cards). Furthermore, Venezia ranks second only to Roma in terms of cautions received (yellow cards). In stark contrast, Inter stands out for their exemplary discipline, having accrued zero red cards throughout the season. This visualization offers a valuable tool for analyzing disciplinary trends across Serie A. By contrasting teams like Venezia, who adopted a more aggressive approach reflected in their red card count, with Inter Milan’s success built on a controlled style, this chart provides insights into the various strategies employed to navigate the competitive intensity of Serie A.

``` r
viz_cards <- data2 %>% 
  rename(yellow_cards = CrdY, red_cards = CrdR) %>% 
  select(Squad, yellow_cards, red_cards)

viz_cards <- pivot_longer(viz_cards, cols = 2:3,
                          names_to = "cards",
                          values_to = "number_of_cards")

viz_cards$cards <- recode(viz_cards$cards, 
                          `yellow_cards` = "Yellow Cards",
                          `red_cards` = "Red Cards")

ggplot(viz_cards, aes(y = fct_reorder(Squad, number_of_cards), 
                      x = number_of_cards,fill = cards))+
  geom_col(position = "dodge") +
  labs(x = "Cards recieved",
       y = NULL,
       title = "Inter finished the season with no player sent off",
       subtitle = "Venezia with the highest number of bookings",
       caption = "Source: football-data.co.uk"
       ) +
  scale_fill_manual(values = c("Red Cards" = "red", "Yellow Cards" = "#EED202")) +
  theme_minimal()+
  guides(fill = "none")
```

<img src="/en/posts/2024-04-17-serie-a-2021-22-a-data-visualization-case-study/index.en-gb_files/figure-html/visualization_6-1.png" width="672" />
