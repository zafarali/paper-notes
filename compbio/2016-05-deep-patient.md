# [Deep Patient: An Unsupervised Representation to Predict the Future of Patients from the Electronic Health Records](http://www.nature.com/articles/srep26094)
* Riccardo Miotto, Li Li, Brian A. Kidd & Joel T. Dudley *

TLDR: Attempt to summarize patient history of medications, diagnoses, tests and procedures (Electronic Health Records, EHR) into a compressed vector representation learned by stacked denoising autoencoders (SDAs). This is similar to an embedding like word2vec for EHRs.

## Method

### Preprocessing
1. Use of regex and ontology software to extract clinical descriptors and normalize brands, dosages and standardized disease codes.
2. Topic modelling for transcribed notes to extract freeform information
2. Normalize clinical descripters to [0, 1] and preserve 0s.


#### Equations

Patient Descriptors `x` a `[0,1]^d` input vector:

1. `y = f(x) = sigmoid(W*(x) + b)`
2. `z = g(x) = sigmoid(W'*y + b')`

`W' = W^T` so that the weights are tied.

Objective: 

`-sum( x *log z + (1-x)*log(1-z))` over all `d` elements of `x` and prediction `z`.

Optimization:

Minibatch stochastic gradient descent (SGD)


### Method

1. (Masking Noise Algorithm:) Add noise by setting a random fraction of descriptors/input vector `x` to zero.
2. Feed into a 3 layer autoencoder with sigmoid activation functions. 
3. Minimize reconstruction error via cross-encropy loss function.
4. Then obtain `f` as the encoding. We apply `f(x)` to obtain the embedding.

They used 500 features as the hidden layer size which means that the final encoding is a desne 500-dimensional vector.

### Dataset

From Mount Sinai data warehouse.

704,587 training patients and after removing frequent/rare clinical descriptors 41k features for training.  76k test patients. For predictive power test: 78 diseases.

### Evaluation and results

Use patient data upto Dec 2013 as training data (34 years of information) and the diagnoses in 2014 as the testing data in two tasks.



#### Task 1: Global Disease Prediction

Use the embeddings to train a random forest classifier to predict one of the 78 diseases in the next year. Measure how well you assign diseases to the patients.

**Metrics**

- AUC-ROC: area under a curve of true pos vs false pos
- F-score: harmonic mean of precision (# of correct pos / # of all pos) and recall (# of correct pos / # pos that should hav been)
- accuracy: proportion of correct classifications

They compared their embedding to PCA, ICA, K-means, GMM and Raw representations.

Found that F-score improves by 54% from raw to proposed. Also proposed embedding outperforms other feature learning methods (Table 2: deep patient improves AUC-ROC by 6-10% over PCA. Curiously, PCA doesn't improve over raw in most cases except prostate cancer)

#### Task 2: Per-patient disease predcition

Predictions in smaller time interval (30, 60, 90, 180 days) and top-k disease scores.

**Metric**

Prec@k: # correct diseases within top k disease scores

## Future / Significance

1. New predictive modelling technique independent of traditional statistical methods like PCA/ICA etc. or hand-crafted methods
2. Non-disease specific embeddings were learned as shown by the future disease classificaion task. 
3. Some diseases do not show predictive power, maybe disease-specific embeddings can also be learned?
4. Did not use lab test result but the number of times the test was done. In the future when everything is electronic, this will be an important (?) predictive feature?

**Potential Applications:** personalized prescriptions and therapy recommendation.

## Thoughts

1. Masking Noise algorithm resembles missing values that might be missing in patients. However, is it justified to use "missing at random" assumption? Maybe a better masking algorithm can be made up to resemble missing value patterns in the dataset?
2. Nice use of the top-k metric "Prec@k". Definitely useful to measure occurences of diseases where the second most likely could be more dangerous than the first most likely and would need to be considered accordingly. 
3. Authors note that the neural network based architecture is not interpretable and would like to work more to find out why the embeddings are the way it is. I'm not sure how possible this is other than for example with GloVe embeddings visualizing them and seeing how they cluster or with algebra. Not clear how to make it interpretable.
4. In point 3 of *signficiance* the authors metion that some diseases do not have predictive power and relate it to lab test frequency vs test result. Maybe the way to deal with this is something like fine-tuning a network?
