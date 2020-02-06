---
layout: post
title: RHR Predictions
---

### Setup
About a month ago I [posted a blog](https://nealwhitlock.github.io/2020-01-10-2019-Fitbit-Data-Exploration/) about exploring some data from my Fitbit tracker over the course of 2019.   I drew special attention to how my activity level seemed to have an inverse relationship with my RHR; that is, as my fitness level increased over time my RHR tended to decrease. 

From that exploration I noted that my activity calories (the calories I burned above my BMR through moving around) appeared to impact my RHR over the next few days. Below is a figure showing the amount of activity calories I burned in a day along the x-axis, how many minutes in that day were considered very active (raising my heart rate to cardio levels and above) along the y-axis, and color coded as what my RHR was the next day. 

<p align="center">
  <img width="700" height="450" src=https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/day_1_rhr_minutes_very_active_to_day_2_rhr.png>
</p>

While not clearly separated into clusters, it appears that the upper portion of this graph contains a higher concentration of pink to purple points (low RHR) while the bottom portion has more yellow to orange points (high RHR). What this says to me is that there is likely a connection between the intensity of my exercising (more very active minutes) and a lower RHR.

### Drawing Connections

To better investigate this connection I added 9 months of data from 2018 and 1 month of 2020 for a total of 22 months of Fitbit data. Early on I noticed despite what features I engineered in my dataset that by far the largest predictor of what my RHR would be is what my RHR was. That makes some sense as there would likely be a problem you should seek professional help with if your heart regularly made jumps from, say, 55 BPM to 65 BPM one day and then back again. This can be seen in the figure below.

<p align="center">
  <img width="700" height="450" src=https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/day_1_rhr_activity_calories_to_day_2_rhr.png>
</p>

Here I have plotted my RHR and activity calories for a given day while the color represents my RHR for the day after. In general, having a low RHR tends to have a similar RHR the next day and the same goes for a higher RHR having a high RHR the next day. The connections begin to separate after a few days, though, as my RHR tended to move around a bit. Here is a figure just like the one above except that the colors match to my RHR 4 days after instead of just the next day.

<p align="center">
  <img width="700" height="450" src=https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/day_1_rhr_activity_calories_to_day_5_rhr.png>
</p>

The colors are beginning to mix with each other, meaning that if I tried to predict my RHR several days out from a given day I would quite likely be wrong. I would need to factor in the days leading up to a predicted day. At least, to train my model I would. Here I have a figure that has the RHR of two consecutive days plotted against each other with the color coded to the RHR of the third day.

<p align="center">
  <img width="700" height="450" src=https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/day_1_rhr_and_day_2_rhr.png>
</p>

### Model Time

To begin predictions I took the mean of my RHR from the data and chose mean absolute error (MAE) as my error metric. The baseline MAE was off by **2.32 BPM**. 

I then made two models to predict with: linear regression and random forest regressor.

The linear regression had a MAE of **0.68 BPM** compared to the test data. This was a drop of almost 71% in error from the baseline.

The best random forest model I came up with after tuning the hyperparameters had a MAE of **0.70 BPM**, a drop of 70% from the baseline.

This graph shows the actual RHR for day 5 and both of the predictions for linear regression (red) and random forest (green).

<p align="center">
  <img width="707" height="604" src=https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/predictions_and_actual.png>
</p>
  
### Conclusion

I realize that this is a very basic model for prediction that is lacking in crucial information relating to RHR; such as hydration level, stress, diet, and so on. Despite that, this was a very enlightening project in that I learned a lot; one of those things being that I recognize I still have a great deal more to learn. For example, why do my predictive models lag a day behind as seen in the model comparison graph? I spent some time looking into time series analysis (TSA), but realized I need to invest much more time into that than I had available. I’m not sure that TSA will lead to a model capable of better predicting my RHR but it certainly won’t hurt me to follow that line of investigation. With more data I also wonder if I can make a model using logistic regression and similar classification predictors. My initial attempt at this with the data I have was horrendously awful. And it still surprises me how much these models use the RHR of a given day to predict the RHR of the following day. When I set out I completely expected activity and sleep to be the deciding factor on the RHR. 

# Addendum

I know that I haven't yet even published this blog post so adding an addendum isn't necessary. After all, I could just revise my original post. However, I feel like it is worth acknowledging the work that I did above and how it relates to this addendum. 

You see, I was ready to call this blog post good and I was feeling pleased with myself about having error rates that were better than the baseline average. Before publishing I decided to walk away and go on a walk. I was pondering on the lag that was showing in the comparison graph above and I couldn't figure out why it was there; what in **both** of my models would cause that to show up? I'm still pondering that but then I recalled that I could try shifting my predictions backward to get rid of the lag and see how that impacted my predictions and errors.

<p align="center">
  <img width="707" height="604" src=https://github.com/NealWhitlock/NealWhitlock.github.io/blob/master/img/predictions_and_shift.png>
</p>

That is much better! I also decided to round my predictions since the values for my RHR from Fitbit are integers and check the MAE for both linear regression and random forest. They are an order of magnitude better than the baseline, now.

Linear Regression MAE: **0.205 BPM**

Random Forest MAE: **0.197 BPM**

I'm not entirely sure right now why these are so much better so I am not ready to call this project complete. It is something I will be returning to in the future when I have more knowledge and experience.
