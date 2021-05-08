# Project 4 Plan - Due May 7
#### Final Project Description

#### Problem Statement: I would like to contribute to existing research focused on developing a name classifier which uses machine learning techniques to classify names according to ethnicity. I began working on this research project during in an independent study in computational sociology that I pursued with Professor Bakhtiari, a professor in William & Mary's sociology department. This research is part of his overarching investigation into health populations for Arab populations in the United States. The problem inspiring the development of the existing method is the difficulty in studying health outcomes for Arab populations due to a lack of specific ethnicity data in existing administrative records. That is, people of Arab descent are often classified as "white", so unless they are foreign born and their country of origin is listed, it is nearly impossible to study health outcomes for this population using records - unless one is able to employ the use of a classification method.  Below is a snippet from a paper I wrote which explains the need to develop this classifier specifically to study the efffect of racism at the population-level for Americans of Arab desent.

"This research is important because it contributes to knowledge on health
outcomes for understudied populations including people of Middle Eastern, North
African, and South Asian descent who are living in America. It is important to
understand the health outcomes for these populations because they are presented with
unique health barriers and stressors such as discrimination based on racialization and
as a result face unique health disparities. The tool developed in this research will serve
future researchers in their studies of health disparities.
More specifically, this research contributes to knowledge about how
discrimination has affected these populations. After the September 11 (hereafter
referred to as 9/11) attacks, people who were racialized as Muslim or Arab faced severe
social consequences including discrimination, harassment, violence, and hate crimes
(Lauderdale 2006; Bakhtiari 2020). Birth outcomes which have been used as a measure
of discrimination include preterm births and low weight births; sociologists have

theorized and to some extent demonstrated that the stress induced by discrimination
can cause both, and both negatively impact the child’s health, further perpetuating
health disparities (Lauderdale 2006).  It is important to study the impact of
discrimination-induced stress on health to do justice to the scholarship about the post-
9/11 time period and to help people better understand the effects of anti-Arab and anti-
Muslim sentiment on both adults and newborns in America.
The objective of this study is to create a name classifier which will aid in the
investigation of birth outcomes before and after 9/11. This name classifier will be helpful
for this project. It may also be helpful for other future projects related to classification of
ethnicities. It could be applied to same or different groups and contexts where ethnicity
data is unavailable."


#### I previously worked on a long short term memory model which trains on list of names and the associated ethnicities or countries of origin and then can be used to classify names inputted. 

#### For this project, I have several potential ideas to either develop a completely new method with which to to compare the existing model or perhaps improve on the long short term memory model I have already used.

#### A description of data: I am using a dataset which includes 148,275 names scraped from Wikipedia and the race or ethnicity associated with each name. There are three variables for each observation in the dataset: first name, last name, and race/ethnicity classification. This dataset was collected by Steven Skiena's research team with the same end goal of building a name classifier. The classes of race/ethnicity are: 'GreaterEuropean,WestEuropean,Germanic', 'GreaterAfrican,Muslim', 'GreaterEuropean,WestEuropean,Nordic', 'GreaterEuropean,WestEuropean,Hispanic', 'GreaterEuropean,Jewish','GreaterAfrican,Africans', 'Asian,GreaterEastAsian,Japanese', 'GreaterEuropean,WestEuropean,French','GreaterEuropean,EastEuropean', 'GreaterEuropean,British','Asian,GreaterEastAsian,EastAsian', 'Asian,IndianSubContinent','GreaterEuropean,WestEuropean,Italian'. I have condensed all the variations of European ethnic grouping to a single European class because I think it is ridiculous to have such a high degree of specificity for the European class and no specificity for the other classes. Perhaps this is a result of the dataset being skewed towards European names, but to me it seemed almost racist when there are equally many or even more categories within other classes (for example, the Asian or African classes could have been subdivided as well). 

#### Machine learning method: I am planning to use a boosted trees model to predict names for my data. I will then compare its performance to the long short term memory model I have previously used as a name classifier. 

#### I will process the Wikipedia dataset using the following code:
path_to_data = 'https://raw.githubusercontent.com/appeler/ethnicolr/master/ethnicolr/data/wiki/wiki_name_race.csv'
data = pd.read_csv(path_to_data)
X_names = ['name_last', 'name_suffix', 'name_first', 'name_middle', 'race']
df = pd.DataFrame(data.values, columns = X_names)
df["race"].replace({"GreaterEuropean,British": "European", "GreatEuropean,EastEuropean": "European", "GreaterEuropean,Jewish": "European", "GreaterEuropean,WestEuropean,French": "European", "GreaterEuropean,WestEuropean,Germanic" : "European", "GreaterEuropean,WestEuropean,Hispanic": "European", "GreaterEuropean,WestEuropean,Italian": "European", "GreaterEuropean,WestEuropean,Nordic": "European"}, inplace=True)

sdf['name_first'] = sdf.name_first.str.normalize('NFKD').str.encode('ascii', errors='ignore').str.decode('utf-8')
sdf['name_last'] = sdf.name_last.str.normalize('NFKD').str.encode('ascii', errors='ignore').str.decode('utf-8')

sdf.groupby('race').agg({'name_first': 'count'})
sdf['name_last_name_first'] = sdf['name_last'] + ' ' + sdf['name_first']

#### This will give me a dataset of over 140,000 observations with first and last names and race/ethnicity associations. 
#### I will then split the Wikipedia dataset to create training and testing data.

X_train,  X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=21, stratify=y)

#### I will then modify the following boosted trees model to take my data as inputs and then eventually predict the appropriate ethnicity given a first and last name. I will start by using a subset of the Wikipedia dataset and trying to predict one class of ethnicity and eventually I will expand the model so that it will be able to predict multiple classes.

fc = tf.feature_column
CATEGORICAL_COLUMNS = ['first_name', 'last_name']

def one_hot_cat_column(feature_name, vocab):
  return fc.indicator_column(
      fc.categorical_column_with_vocabulary_list(feature_name,
                                                 vocab))
feature_columns = []
for feature_name in CATEGORICAL_COLUMNS:
  vocabulary = dftrain[feature_name].unique()
  feature_columns.append(one_hot_cat_column(feature_name, vocabulary))

NUM_EXAMPLES = len(y_train)

def make_input_fn(X, y, n_epochs=None, shuffle=True):
  def input_fn():
    dataset = tf.data.Dataset.from_tensor_slices((X.to_dict(orient='list'), y))
    if shuffle:
      dataset = dataset.shuffle(NUM_EXAMPLES)
    dataset = (dataset
      .repeat(n_epochs)
      .batch(NUM_EXAMPLES))
    return dataset
  return input_fn
  
train_input_fn = make_input_fn(dftrain, y_train)
eval_input_fn = make_input_fn(dfeval, y_eval, shuffle=False, n_epochs=1)

params = {
  'n_trees': 50,
  'max_depth': 3,
  'n_batches_per_layer': 1,
  'center_bias': True
}

est = tf.estimator.BoostedTreesClassifier(feature_columns, **params)

results = est.evaluate(eval_input_fn)
clear_output()
pd.Series(results).to_frame()

in_memory_params = dict(params)
in_memory_params['n_batches_per_layer'] = 1
def make_inmemory_train_input_fn(X, y):
  y = np.expand_dims(y, axis=1)
  def input_fn():
    return dict(X), y
  return input_fn
train_input_fn = make_inmemory_train_input_fn(dftrain, y_train)

est = tf.estimator.BoostedTreesClassifier(
    feature_columns, 
    train_in_memory=True, 
    **in_memory_params)

est.train(train_input_fn)
print(est.evaluate(eval_input_fn))

#### If I have extra time, I will incorporate the following code as well. The following code splits the names into bigrams (two-letter segments) to make it easier for the model to predict names. Certain bigrams are associated with certain ethnic classifications, so taking this additional step could be really helpful. I use bigrams for the long short term memory model as well, so it would make it easier to compare the boosted trees if it was run on bigrams.

NGRAMS = 2

vect = CountVectorizer(analyzer='char', max_df=0.3, min_df=3, ngram_range=(NGRAMS, NGRAMS), lowercase=False) 
a = vect.fit_transform(sdf.name_last_name_first)
vocab = vect.vocabulary_

words = []
for b in vocab:
    c = vocab[b]
    #print(b, c, a[:, c].sum())
    words.append((a[:, c].sum(), b))
    #break
words = sorted(words, reverse=True)
words_list = [w[1] for w in words]
num_words = len(words_list)
print("num_words = %d" % num_words)

def find_ngrams(text, n):
    a = zip(*[text[i:] for i in range(n)])
    wi = []
    for i in a:
        w = ''.join(i)
        try:
            idx = words_list.index(w)
        except:
            idx = 0
        wi.append(idx)
    return wi

X = np.array(sdf.name_last_name_first.apply(lambda c: find_ngrams(c, NGRAMS)))

X_len = []
for x in X:
    X_len.append(len(x))

max_feature_len = max(X_len)
avg_feature_len = int(np.mean(X_len))

print("Max feature len = %d, Avg. feature len = %d" % (max_feature_len, avg_feature_len))
y = np.array(sdf.race.astype('category').cat.codes)


#### Preliminary Assessment: 

#### The inspiration for this project is the research I have assisted with in the sociology department for the past academic year (2020-2021). Below is another snippet from the paper I wrote and referenced above; the following snippet provides some background on previous approaches that researchers have taken to address the issue of name classification.

"Torvik and Agarwal (2016) developed Ethnea, a method of name classification
using a nearest neighbors approach, on a data set containing millions of records from
the PubMed database. Records include author names coupled with the country or
countries their article is associated with. Unlike other classification methods, there is no
machine learning involved. Rather, the model uses the dataset of author names which

are respectively mapped to countries of publication. They use a logistic regression
model to map the country distribution to ethnicities. Torvik and Agarwal (2016) were
able to generate predictions for millions of first and last names that were found in the
database. To determine the ethnicity of a first and last name combination, the method
would combine the probabilities and respective ethnicities associated with each. As a
result, millions of names can be classified using the instances present in the data set.
Although it accomplishes a similar goal, the Ethnea method is not appropriate for this
research because its classifications tend to represent nationality rather than ancestry,
due to the nature of the PubMed data it was developed from.
Ye et. al (2017) worked to develop a sophisticated nationality and ethnicity
classifier to aid research which requires this demographic information that is often
unavailable in data sources. Names can be used as an indicator of ethnicity and
nationalities, and there are various methods to develop models which can accurately
classify new names. Ye et. al (2017) used the idea of homophily and name embeddings
to develop NamePrism, a classifier which classified names according to nationality and
ethnicity. The idea of homophily is that people interact with others similar to them. Ye et
al. (2017) built this classifier using an online company’s list of 57 million contact lists and
assortment of 74 million names. The name embeddings were developed from the
contact lists and based on the idea of homophily; they can be used to map out names
according to nationality. NamePrism includes thirty-nine nationalities which together
represent more than 90% of the world population (Ye et al. 2017). It was extremely
successful; it was an improvement upon similar classifiers that had previously been
used (Ye et al. 2017). The researchers applied it to the followers of Twitter celebrities to

illustrate the ethnic makeups of different celebrities’ followers. Although NamePrism
could theoretically have been used to answer this research question, upon some
preliminary testing, it was unable to classify Middle Eastern and North African names
adequately enough for the purposes of this study.
Sood and Laohaprapanon (2018) sought to examine racial inequality in the news
and political campaigns. To accomplish this, they decided they needed a method to
classify names based on race and ethnicity. They used the 2017 Florida Voter
Registration Data and Wikipedia to train a Long Short Term Memory Network model to
predict the race and ethnicity associated with the name based on character sequences.
The voter registration data contained the name and associated race of nearly 13 million
voters (Sood and Laohaprapanon 2018). The Wikipedia data was collected by another
research team for a similar classification project; the team scraped Wikipedia and
collected 140,000 names with the associated races (Sood and Laohaprapanon). The
Long Short Term Memory Network model’s out of sample precision for a model using
full names is .83. Using campaign finance data, they applied the model to predict the
race of donors to better understand the proportion of each racial group which donates to
campaigns. Sood and Laohapranon (2018)’s work with the Long Short Term Memory
model inspired my use of it for this name classification project. Although the research
question differs, the general goal of using a model to determine the ethnicities
associated with names in a dataset is the same."

#### For this project, I thought it would be interesting to apply a new method, which I learned about and used in this Machine Learning class: the boosted trees model. I wanted to include the context from my paper to give some background on methods already implemented. Now I will discuss some similar implementations of the boosted trees model. Although based on my literature review, the model has not been used exactly the way I plan to use it, the following articles have provided a general framework.

#### One informal article I found online detailed the application of a boosted trees model for optimizing in the field of natural language processing (NLP). The researcher, Thomas Atanasov, used Kaggle data of Honda reviews to create a model which would determine ratings based on the text contained in the review. The dataset contained three variables per observation: an id, a review (text), and rating (1-5). Atanasov utilized XGBoost, an "optimized distributed gradient boosting library" which "implements machine learning algorithms under the Gradeient Boosting Framework." He optimized using the arguments "max_depth" which refers to the max depth of a tree and eta, which prevents overfitting by altering weights. Atanasov's training set contained 10k observations with a feature of the text from the review and the target was the rating (a number one through five). My goal is similar, in a sense, to Atanasov's: my feature is also textual (names) and my target also contains several categories (ethnicity), although my categories are not numerical. Atanasov then 

#### Sources: 
- https://link-springer-com.proxy.wm.edu/article/10.1007/s11192-012-0681-1
- https://rstudio-pubs-static.s3.amazonaws.com/430967_3343f1ee97234612b78582c49c49007e.html
- https://github.com/appeler/ethnicolr/tree/master/ethnicolr/data/wiki
- https://github.com/appeler/ethnicolr
