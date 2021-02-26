### Calculating MSE and MAE 
#### The mean squared error for all the predictions is 281726.7. The mean absolute error for all predictions is 232.1. 
#### The mean squared error for the 10 biggest over-predictions is 198050.7. The mean absolute error for the 10 biggest over-predictions is 444.1. 
#### The mean squared error for the 10 biggest under-predictions is 9073386.6. The mean absolute error for the 10 biggest under-predictions is 2401.4.  
#### Analysis: The fact that the MSE for the 10 biggest over-predictions is less than the MSE for all the predictions demonstrates that the over-predictions were not that severe compared with the under-predictions. The MSE and MAE for the 10 biggest over predictions is very large compared with those for all predictions and even those for the under predictions. It seems especially large when considering that the actual and predicted price are in units of thousands of dollars. However, when looking closer at these values, it seems that the houses that were undervalued by the model are worth millions in real life; I suspect that this is caused by geographic location rather than a physical attribute of the house. Geographic location makes a significant contribution to house value especially in such extreme cases.

#### In which percentile do the 10 most accurate predictions reside? Did your model trend towards over or under predicting home values?

#### Which feature appears to be the most significant predictor in the above cases?
#### I think that geography is likely a significant predictor. I hope to have the time to incorporate it as a variable in my final project. 

#### Stretch goal: calculate the MAE and compare with your MSE results
