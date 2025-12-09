---
layout: default
---

# Behaviour During the Year: Finding Seasonality in ESG and Non-ESG ETFs

Does an energy ETF perform better in winter? Does a travel and leisure ETF shine in summer? To answer questions like these, we’ll explore whether seasonality affects ETF performance—and whether those patterns repeat year after year. The goal is to move from what to invest in, to when to invest in what.

## A First Look

To get a high-altitude, eagle-eye view of ETF behaviour over time, we started by building a heatmap that shows the average price of each ETF for every month across all available years
.
<iframe src="https://pablovallbona.github.io/ada-template-website/assets/graphs/monthly_heatmap.html" width="100%" height="600px" style="border:none;"></iframe>

At first glance, this broad overview doesn’t give us much to work with no obvious seasonal pattern jumps off the screen. So, we decided to break the year into smaller segments to see whether seasonality becomes more visible when time is grouped more coarsely than by month.

<iframe src="https://pablovallbona.github.io/ada-template-website/assets/graphs/heatmap-split6.html" width="100%" height="600px" style="border:none;"></iframe>

Wow... still no dramatic revelation. If we squint (and maybe tilt our head a little), we might convince ourselves that performance is slightly higher in the second and sixth segments of the year. But making investment claims based on colour gradients and imagination wouldn't be very "data-scientist" of us. So instead, we’ll do this properly by running a t-test to check whether the average returns in these periods truly differ in a statistically significant way, rather than just appearing so visually.

## A Deeper Look

Ok, let's put our scientist lab coats on, (even if data scientists don't have lab coats). We will keep on looking at the whole ETF market for the time being. 

<img src="assets/img/heatmap_all_etf_average.png" alt="intro_image" style="width:20%;max-width:1000px;display:block;margin:auto;">

Here, we see the average z-scores for each period of the year. It turns out we were wrong about the second period which is actually below the overall average. On the other hand, the last period, representing November and December, shows stronger performance, likely reflecting the impact of Black Friday and the Christmas shopping season. We also observe higher performance during the third and fourth periods, corresponding to the summer months from May to August. This is a bit surprising, as it seems to contradict the _“Sell in May and go away”_ adage, where stocks are typically sold off during the summer, causing market performance to dip. However, ETFs in the travel, leisure, and energy sectors, boosted by summer vacations and gasoline demand, could explain this upward trend.
That said, to make any confident claims, we need to check the statistical significance of these results. The patterns are interesting, but only a proper significance test can tell us whether they are meaningful or just random noise.