#### Project 1, due Wednesday, March 3rd by noon
#### Select a city and scrape as many observations as possible from zillow. Try to obtain at least 400 observations from your selected location.
#### I selected Madison, Wisconsin and scraped 400 observations. 
#### Clean the housing data you obtained and create a number of usable features (independent variables) and targets (dependent variables). Set price as the response variable, and then set numbers of beds, number of bathrooms and total square footage as the predictors.
#### After importing the data into my project using the Python 3.8 interpreter, I opened to read through to look for potential issues. I saw a few en dashes spaced throughout and decided that I would replace these en dashes with a value of zero for my model. Although this is not the most accurate approach, I figured that because there were not many missing values, it would not make a big difference in the overall performance of the model. 
#### I then assigned an x(i) for each of the features. X1 is number of bedrooms, X2 is number of bathrooms, X3 is square footage, and X4 is zip code. I created a colum in the data frame for the zip code associated with each home bt splitting the string of the address after the state because zip code is always listed after state. I created a target variable which contained house prices. I made all the features and the target float types.
#### I then normalized my data by dividing my x3, which represents square footage, by 1000 and y, house prices, by 100000.
#### I created a Sequential model with one dense layer that takes an input shape equal to 4. (ELABORATE) I compiled my model with a stochastic gradient descent optimizer and a loss function using mean squared error. 
#### Following the previous model you specified (6 houses in Mathews), import your new data set and train a new model on your target and features.
#### Write a one and a half to two page report on your results and include the following.
#### A description of the housing data you scraped from zillow
#### A description of your model architecture
#### An analysis of your model output
#### An analysis of the output that assesses and ranks all homes from best to worst deal
#### Include at least three plots that support your project report
#### Stretch goal: add a spatial variable to your feature set and compare with the original model. Did this improve the predictive power of your model? If so, how?
