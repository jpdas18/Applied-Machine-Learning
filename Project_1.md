#### Project 1
#### **Task #1: Pick a city, scrape as many observations from Zillow**
#### I chose to examine **Madison**, the capital of Wisconsin. I first scraped exactly **400 observations**, generating a comma separated values (.csv) file which I named homes.csv. To understand the data a little bit better, I looked at some basic stats.

| | Beds | Bathrooms | Square Footage | Price |
| ---- | ---- | ---- | ---- | ---- |
| Mean | 2.99 | 2.29 | 1814.95 | 467777.33 |
| Median | 3 | 2 | 1544 | 350000 | 
| Max | 6 | 5 | 5330 | 12999000 | 
| Min | 1 | 1 | 45 | 37000 |

#### There is a wide range of house prices in the dataset. The mean house price is greater than the median; there are likely some expensive outliers that skew this mean above the median. The mean and median of the number of bedrooms are approximately equal, indicating that the data is fairly symetric with respect to this variable. The mean and median for number of bathrooms are also close together. There is a decent sized disparity between the mean and median for square footage; the mean is greater by 300, which means there are probably some larger homes skewing the data. 
#### I imported homes.csv into a PythonProject that uses Python 3.8 because in my next steps I will be using the tensorflow library, which runs best in this Python version. However, before applying a machine learning model, it is necessary to clean the data.

#### **Task #2: Clean the housing data you obtained and create a number of usable features and targets.**
#### After importing the data into my project using the Python 3.8 interpreter, I opened it to read through, looking for potential issues. I saw a few en dashes spaced throughout and decided that I would replace these en dashes with a value of zero for my model. I chose to replace the missing data with values of zero. Although this is not the most accurate approach, because there were not many missing values, I posited that it would not make a big difference in the overall performance of the model. I then removed html tags.
#### I then created new vectors for each variable of interest. Beds, number of bedrooms, square footage are my features and predictors of house price. X1 is number of bedrooms, X2 is number of bathrooms, X3 is square footage, and X4 is zip code. I created a colum in the data frame for the zip code associated with each home bt splitting the string of the address after the state because zip code is always listed after state. I set price as the response variable and target. I converted all this information to the float data type. 
#### I then normalized my data by dividing my x3, which represents square footage, by 1000 and y, house prices, by 100000. One of my reasons for creating columns separate from the dataframe was so I could normalize without changing any values in the dataframe. Now the data should be ready to input into a model.

#### **Task #3: Train a model on your target and features.**
#### I created a Sequential model with one dense layer that takes an input shape equal to 4. I compiled my model with a stochastic gradient descent optimizer and a loss function using mean squared error. 
#### I then stacked my features, x values x1, x2, x3, x4, and fit the model to my features and target. I used ten epochs because I wanted to see if it would run. It turns out that it generated a loss equal to Nan, and when I created a column y_pred for model predictions, all y_pred values equaled Nan. 
#### I decided to go back and remove my variable for zip code to see if my model would work without it. I repeated all the steps, except gave the model an input shape of 3 instead of 4, and only stacked x1, x2, and x3 (excluding zip code). The model worked and generated y_pred values that made sense. I decided to continue working on my project without the zip code variable; I will incorporate it later if/when I have the time.  
#### After seeing that the model ran on the data without the zip code variable, I decided to run the model again but with 100 epochs. However, my model did not improve with increasing epochs, as shown below.

![plot](project1_fig1.png)

#### I decided to try out a different optimizer. I compiled the model with an optimizer equal to adam and 

![plot](project1_fig3.png)

#### I used the model to make predictions for each house price in the dataset. I added a column for predictions to the dataframe, as well as a column for the difference between predicted price and actual price. 

#### A description of the housing data you scraped from zillow
#### A description of your model architecture
#### An analysis of your model output
#### An analysis of the output that assesses and ranks all homes from best to worst deal
#### Include at least three plots that support your project report
#### Stretch goal: add a spatial variable to your feature set and compare with the original model. Did this improve the predictive power of your model? If so, how?
