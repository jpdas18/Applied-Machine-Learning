# Project 4 Plan - Due May 7
#### Final Project Description

#### Problem Statement: I would like to contribute to existing research focused on developing a name classifier which uses machine learning techniques to classify names according to ethnicity. I began working on this research project during in an independent study in computational sociology that I pursued with Professor Bakhtiari, a professor in William & Mary's sociology department. This research is part of his overarching investigation into health populations for Arab populations in the United States. The problem inspiring the development of the existing method is the difficulty in studying health outcomes for Arab populations due to a lack of specific ethnicity data in existing administrative records. That is, people of Arab descent are often classified as "white", so unless they are foreign born and their country of origin is listed, it is nearly impossible to study health outcomes for this population using records - unless one is able to employ the use of a classification method. 

#### I previously worked on a long short term memory model which trains on list of names and the associated ethnicities or countries of origin and then can be used to classify names inputted. For this project, I have several potential ideas to either develop a completely new method with which to to compare the existing model or perhaps improve on the long short term memory model I have already used.

#### A description of data: I am using a dataset which includes 148,275 names scraped from Wikipedia. These names are already classified by ethnic grouping. The ethnic groups are: 'GreaterEuropean,WestEuropean,Germanic', 'GreaterAfrican,Muslim', 'GreaterEuropean,WestEuropean,Nordic', 'GreaterEuropean,WestEuropean,Hispanic', 'GreaterEuropean,Jewish','GreaterAfrican,Africans', 'Asian,GreaterEastAsian,Japanese', 'GreaterEuropean,WestEuropean,French','GreaterEuropean,EastEuropean', 'GreaterEuropean,British','Asian,GreaterEastAsian,EastAsian', 'Asian,IndianSubContinent','GreaterEuropean,WestEuropean,Italian'. I have condensed all the variations of European ethnic grouping to a single European group. 

#### Machine learning method: I am planning to use a boosted trees model to predict names for my data. I will then compare its performance to the long short term memory model I have already created and used. 
## Will be updated by end of day May 8