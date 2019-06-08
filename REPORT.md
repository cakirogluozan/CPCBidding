# CPCBidding Report
CPC Bidding for A Booking Website with Machine Learning Methods. 

Context:

- Walking through process
- Data Preprocessing
- Data Analysis
- Feature Selection
- Modeling
- Performance Evaluation
- Conclusion

## Walking through process.

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

## Data Analysis

Before continuing with the modeling and feature selection, distribution of the (total) number of clicks is analyzed in the following in order to support my assumption which states click distribution over dayofweek, day, week and month has a valuable feature information. For example, number of clicks at Christmas should be lower value than average and number of clicks at summer time should be higher value than average etc.

In the following graph, average number of clicks over day of week is visualized. It can be concluded that average clicking has its highest value on Sunday and lowest value on Friday.
  - {0: Monday, 1: Tuesday, ... 5: Saturday, 6: Sunday}
  - {Black: Monday-..-Thursday, Red: Friday, Blue: Saturday, Green: Sunday}
  
![](/imgs/clicks_dayofweek.png)   

In the next figure, average number clicks over week is plotted. 

![](/imgs/clicks_week.png)

As you can see, even though the data includes lots of rows, (or inputs) average values are lower floats in between 0-2. In order to find the reasoning behind low average and analyse click distribution over each (dayofweek, week) combination, total count of zero clicks is plotted.

  - {Black: Monday-..-Thursday, Red: Friday, Blue: Saturday, Green: Sunday}

![](/imgs/zero_clicks.png)

In the following two figures, click distribution over (dayofweek, week) is plotted. The only difference is for analyzing behaviour of average value over dayofweek-ly and monthly, respectively.

  - {Black: Monday-..-Thursday, Red: Friday, Blue: Saturday, Green: Sunday}

![](/imgs/clicks_dayofweekly.png)

  - {Black: January, Red: February, Blue: March}

![](/imgs/clicks_monthly.png)

## Feature Selection

I plotted correlation matrix of the dataframe in order to get information if the features are correlated to each other. Since using correlated features does not affect the performance of the model and training with more features would require much more time, I eliminated some features.

The image below is the correlation matrix of the whole dataframe.

![](/imgs/corr_all_cols.png)

And, chosen features' correlation is plotted in the following.

![](/imgs/corr_features.png)

## Modeling

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

    Random Forest Regressor: n_estimators=20, criterion='mse', outputs=2, inputs=4

At the model, in order to forecast CPC Bid of a hotelier for a given date, the hotelier's previous 4 days of information is used. For instance, in order to evaluate 5th January CPC Bid, 1-2-3-4 January days are used as inputs.

## Performance Evaluation

After fitting, the model's performance is evaluated and plotted for both training and test materials in the following.

![](/imgs/direct_clicks_training.png)

![](/imgs/direct_clicks_test.png)

![](/imgs/direct_cost_training.png)

![](/imgs/direct_cost_test.png)

For more precise performance evaluation, r-squared score is evaluated as 0.649.

    R-squared is a statistical measure of how close the data are to the fitted regression line. It is also known as the coefficient of determination, or the coefficient of multiple determination for multiple regression.

## Conclusion
CPC Bidding is a charging method for booking websites. A hotelier is charged by each click to its offer(s): Cost-per-click. Forecasting future CPC Bids would provide valuable information to booking websites as well as to hoteliers. 

In this project, hotelier's 90 days of data is preprocessed, analyzed and modelled for forecasting future CPC Bids moreover model's performance is evaluated. 

At the end of the project, r-squared score is evaluated as 0.649. 

The score can be improved by 

  1- extracting the number of events, seminars, etc. in the city.

  2- extracting if the date is a holiday such as Christmas, Easter, Spring Break etc.

  3- expanding the dataset by including previous year's, week's, dayofweek's clicks of output. For example: 

| Type | Date | Clicks | 
|---|---|---|
|output| 01.01.2019| Y |
|previous year| 01.01.2018| X1|
|previous week| 25.12.2018| X2|
|day of week| Saturday| X3|
