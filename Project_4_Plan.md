# Project 4 Plan - Due May 7
#### Final Project Description

#### Problem Statement: I would like to contribute to existing research focused on developing a name classifier which uses machine learning techniques to classify names according to ethnicity. I began working on this research project during in an independent study in computational sociology that I pursued with Professor Bakhtiari, a professor in William & Mary's sociology department. This research is part of his overarching investigation into health populations for Arab populations in the United States. The problem inspiring the development of the existing method is the difficulty in studying health outcomes for Arab populations due to a lack of specific ethnicity data in existing administrative records. That is, people of Arab descent are often classified as "white", so unless they are foreign born and their country of origin is listed, it is nearly impossible to study health outcomes for this population using records - unless one is able to employ the use of a classification method. 

#### I previously worked on a long short term memory model which trains on list of names and the associated ethnicities or countries of origin and then can be used to classify names inputted. For this project, I have several potential ideas to either develop a completely new method with which to to compare the existing model or perhaps improve on the long short term memory model I have already used.

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

# concat last name and first name
sdf['name_last_name_first'] = sdf['name_last'] + ' ' + sdf['name_first']

#### This will give me a dataset of over 140,000 observations with first and last names and race/ethnicity associations. 
#### I will then split the Wikipedia dataset to create training and testing data.

# Split train and test dataset
X_train,  X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=21, stratify=y)

# I will then modify the following boosted trees model to take my data as inputs and then eventually predict the appropriate ethnicity given a first and last name.

fc = tf.feature_column
CATEGORICAL_COLUMNS = ['first_name', 'last_name']

def one_hot_cat_column(feature_name, vocab):
  return fc.indicator_column(
      fc.categorical_column_with_vocabulary_list(feature_name,
                                                 vocab))
feature_columns = []
for feature_name in CATEGORICAL_COLUMNS:
  # Need to one-hot encode categorical features.
  vocabulary = dftrain[feature_name].unique()
  feature_columns.append(one_hot_cat_column(feature_name, vocabulary))

# Use entire batch since this is such a small dataset.
NUM_EXAMPLES = len(y_train)

def make_input_fn(X, y, n_epochs=None, shuffle=True):
  def input_fn():
    dataset = tf.data.Dataset.from_tensor_slices((X.to_dict(orient='list'), y))
    if shuffle:
      dataset = dataset.shuffle(NUM_EXAMPLES)
    # For training, cycle thru dataset as many times as need (n_epochs=None).
    dataset = (dataset
      .repeat(n_epochs)
      .batch(NUM_EXAMPLES))
    return dataset
  return input_fn

# Training and evaluation input functions.
train_input_fn = make_input_fn(dftrain, y_train)
eval_input_fn = make_input_fn(dfeval, y_eval, shuffle=False, n_epochs=1)

params = {
  'n_trees': 50,
  'max_depth': 3,
  'n_batches_per_layer': 1,
  # You must enable center_bias = True to get DFCs. This will force the model to
  # make an initial prediction before using any features (e.g. use the mean of
  # the training labels for regression or log odds for classification when
  # using cross entropy loss).
  'center_bias': True
}

est = tf.estimator.BoostedTreesClassifier(feature_columns, **params)
# Train model.
est.train(train_input_fn, max_steps=100)

# Evaluation.
results = est.evaluate(eval_input_fn)
clear_output()
pd.Series(results).to_frame()

in_memory_params = dict(params)
in_memory_params['n_batches_per_layer'] = 1
# In-memory input_fn does not use batching.
def make_inmemory_train_input_fn(X, y):
  y = np.expand_dims(y, axis=1)
  def input_fn():
    return dict(X), y
  return input_fn
train_input_fn = make_inmemory_train_input_fn(dftrain, y_train)

# Train the model.
est = tf.estimator.BoostedTreesClassifier(
    feature_columns, 
    train_in_memory=True, 
    **in_memory_params)

est.train(train_input_fn)
print(est.evaluate(eval_input_fn))

#### If I have extra time, I will incorporate the following code as well. The following code splits the names into bigrams (two-letter segments) to make it easier for the model to predict names. Certain bigrams are associated with certain ethnic classifications, so taking this additional step could be really helpful. I use bigrams for the long short term memory model as well, so it would make it easier to compare the boosted trees if it was run on bigrams.

NGRAMS = 2

# build n-gram list
vect = CountVectorizer(analyzer='char', max_df=0.3, min_df=3, ngram_range=(NGRAMS, NGRAMS), lowercase=False) 
a = vect.fit_transform(sdf.name_last_name_first)
vocab = vect.vocabulary_

# sort n-gram by freq (highest -> lowest)
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

# build X from index of n-gram sequence
X = np.array(sdf.name_last_name_first.apply(lambda c: find_ngrams(c, NGRAMS)))

# check max/avg feature
X_len = []
for x in X:
    X_len.append(len(x))

max_feature_len = max(X_len)
avg_feature_len = int(np.mean(X_len))

print("Max feature len = %d, Avg. feature len = %d" % (max_feature_len, avg_feature_len))
y = np.array(sdf.race.astype('category').cat.codes)

# Split train and test dataset
X_train,  X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=21, stratify=y)


#### Preliminary Assessment: 
