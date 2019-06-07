# CPCBidding
CPC Bidding for A Booking Website with Machine Learning Methods

## Walking through your process.

1- What indicators did I use to make your predictions?

- I've used 'hotel_id', 'stars' directly as provided in the dataset.
- I've used 'locale', 'city', 'country', 'abc_category' columns by encoding them into integers.
- I've used 'rating' column by converting it from object to integer type.
- I've used 'week', 'dayofweek', 'month', and 'day' columns that I extracted from 'ymd' column. 
  - 'week' stands for week of the year      {0,51}
  - 'dayofweek' stands for day of the week  {0,6}
  - 'month' stands for month of the year    {0,11}
  - 'day' stands for day of the month       {0-30}

What difficulties did I encounter and how did you overcome them?

- Modelling was the most difficult part. I believe it is because I've not worked on CPC Bidding or similar data before. Hence I had to understand what is CPC Bidding. I overcome this challenge by researching further and learning the terminology. Then, it was easier to propose a model after getting intuition on the subject.

What methodologies and tools did I use?

- I've used Python 3.5 on Jupyter-Notebook. Moreover;

  - pandas and numpy for data manipulation.
  - os and subprocess for reading data, walking through directories and saving dataframe table images.
  - matplotlib and seaborn for data visualization.
  - scikit-learn for machine learning modelling, data preprocessing and performance evaluation.

## Requirements

I've used Python 3.5 on Jupyter-Notebook. Moreover;

pandas and numpy for data manipulation.

os and subprocess for reading data, walking through directories and saving dataframe table images.

matplotlib and seaborn for data visualization.

scikit-learn for machine learning modelling, data preprocessing and performance evaluation.

## Data Preprocessing

I used pandas library for data manipulation on Jupyter-Notebook. Firstly, I read the csv file and checked if there is any duplicated rows in dataset. After validating that each row is unique, I start manipulation on the raw csv file which is plotted in below.

![](/imgs/csv_table.png)

The first operation is to analyze type of columns and NaN values (if there is any). And analysis dataframe is shown in the following.

![](/imgs/type_nans.png)


#### *'rating' column*

During visualization, I've realized that some hotels do not have any (integer) ratings, they have '\N' value that indicates the hotel is not rated before. I've changed the value into average rating of all hotels.

#### *'locale', 'city', 'country', 'abc_category' columns*

Then, I encoded 'locale', 'city', 'country', 'abc_category' columns with integers which is a necessity for Machine Learning methods. Please, note that I decoded the columns at the final dataframe, as well.

#### *'ymd' column*

'ymd' column contains time information of each campaign. Since the objective of the website is advantagous hotel reservations, time feature would have a huge impact on trainings. However, I assumed using full dates (YYYYmmdd) would not provide 'seasonal' information. Hence, I extracted 'week of the year':{0-51} and 'day of the week':{0-6} values.

After the opearion, encdoded dataframe is as below.

![](/imgs/encoded_csv.png)

And its stats:

![](/imgs/encoded_csv_stats.png)

Before continuing with the modeling and feature selection, distribution of the (total) number of clicks is analyzed in the following in order to support my assumption which states click distribution over dayofweek, day, week and month has a valuable feature information. For example, number of clicks at Christmas should be lower value than average and number of clicks at summer time should be higher value than average etc.

In the following graph, average number of clicks over day of week is visualized. It can be concluded that average clicking has its highest value on Sunday and lowest value on Friday.
  - {0: Monday, 1: Tuesday, ... 5: Saturday, 6: Sunday}
  - {Black: Monday-..-Thursday, Red: Friday, Blue: Saturday, Green: Sunday}
  
![](/imgs/clicks_dayofweek.png)   
  - {Black: Monday-..-Thursday, Red: Friday, Blue: Saturday, Green: Sunday}

In the next figure, average number clicks over week is plotted. 

![](/imgs/clicks_week.png)

As you can see, even though the data includes lots of rows, (or inputs) average values are lower floats in between 0-2. In order to find the reasoning behind low average and analyse click distribution over each (dayofweek, week) combination, total count of zero clicks is plotted.

![](/imgs/zero_clicks.png)

In the following two figures, click distribution over (dayofweek, week) is plotted. The only difference is for analyzing behaviour of average value over dayofweek-ly and monthly, respectively.

![](/imgs/clicks_dayofweekly.png)

![](/imgs/clicks_monthly.png)


I plotted correlation matrix of the dataframe in order to get information if the features are correlated to each other. Since using correlated features does not affect the performance of the model and training with more features would require much more time, I eliminated some features.

The image below is the correlation matrix of the whole dataframe.

![](/imgs/corr_all_cols.png)

And, chosen features' correlation is plotted in the following.

![](/imgs/corr_features.png)

## Modelling

After analyzing the data and preparing the training material, I modelled several machine learning solution for the best CPC bidding algorithm. Firstly, I determined my objective. While the main objective is finding the best CPC bidding, it also requires to keep CPA at 15% level.

    CPC : Cost per Click       : direct_cost/direct_click
    CPA : Cost per Acquisition : direct_booking_value/direct_click

As I formulized above, there are three unknowns to algorithm. I desired to estimate direct_cost and direct_click outputs and tune the direct_booking value to keep at 15%.

Hence, the input data and the output data is shown in the following tables.

#### Input

![](/imgs/features.png)

#### Output

![](/imgs/outputs.png)

Data is splitted into 'training' and 'test' materials. Training material is used for fitting and the model's performance is evaluated with training and test materials. Please note that performance is evaluated with data the model is fitted with, and data the model's never seen before. The split size is 0.25:

After small-sized tests, I've concluded that Random Forest machine learning model with multiple (two) outputs performs the best amongs others.

    Random Forest Regressor: n_estimators=100, criterion='mse', outputs=2

After fitting, the model's performance is evaluated and plotted for both training and test materials in the following.

![](/imgs/direct_clicks_training.png)

![](/imgs/direct_clicks_test.png)

![](/imgs/direct_cost_training.png)

![](/imgs/direct_cost_test.png)

For more precise performance evaluation, r-squared values are calculated and shown in below.

    R-squared is a statistical measure of how close the data are to the fitted regression line. It is also known as the coefficient of determination, or the coefficient of multiple determination for multiple regression.

![](/imgs/r2.png)