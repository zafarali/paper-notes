# [DeepCancer: Detecting Cancer through Gene Expressions via Deep Generative Learning](http://arxiv.org/abs/1612.03211)
* Rajendra Rana Bhat, Vivek Viswanath, Xiaolin Li *


TLDR: Generative Adverserial Networks are used to learn features from mRNA expression profiles. The Generator becomes good at generating fake expression profiles that can fool the discriminator. The internal features from the Discriminator can be used with a new layer on top that will allow classification of cancer vs. non-cancer.

## Method

### Model
After the discriminator learns to detect fake vs real well, pass through sigmoid for supervised classification.


#### Generator:

Activations: Relu

1. Accept 100 dimension noise vector, z. 
2. Reshape, batch-normalize, upsample
3. Multiple convolutions
4. FC layers
4. Convert to a 224x183 matrix as output


#### Discriminator

Activations: LeakyReLU and dropout of 0.5

1. Accept 224x183 matrix
2. Convolution layers
3. flatten
4. FC layer
5. sigmoid for binary classification


Exact architecture shown in Fig 2.

### Dataset

1. *GSE45584 Breast Cancer dataset*: microarray gene expression profiles for 20 inflamatory breast cancer, 20 non-inflamatory breast cancer and 5 normal tissues. 41000 unique genes, expresson levels are floating-point positive real values. 
2. *Prostate Cancer dataset*: expression profiles for 52 cancerous and 50 non-cancerous prostate speciments. Floating positive and negative real values. 

### Evaluation and results

They use a Restricted Boltzmann Machine with Logistic and SVM as baseline models 


Three Tasks:

1. Classification of breast tissue as cancer vs non-cancer
2. Classification of breast cancer as inflammatory vs non-inflammatory.
3. Classification of prostate tissue as cancerous vs non-cancerous/. 

#### Preprocessing

For Task 1: non-cancerous tissues were augmented to balance dataset (8 times). Inflammatory and non-Inflammatory were combined together to form the cancer class.

For Task 2: Remove non-cancerous tissues.

For Task 3: None mentioned.

#### Metrics

Precision-recall: 

1. High precision, low recall => fewer samples detected but all correct.
2. Lower precision, high recall => samples are being misclassified.

Good metric because misclassiciaiton of a inflammatory as a non-inflamatory has worse consequences compared to the other way around. 

#### Results:

1. Generator learns to reproduce gene expression profiles well enough to be classified as true by the discriminator.


## Thoughts
1. Suggests that during adverserial training stage, the discriminator becomes really good at picking up the real features of the data. 
2. Justification of using the convolution netwro kis because it can learn features over different regions + decreases # free learnable parameters
3. Cross validation was only mentioned in passing?
4. Idea of using the layers of the discriminator like that of the autoencoder to feed into a different classifier for classification.

