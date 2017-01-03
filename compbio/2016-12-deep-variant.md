# [Creating a universal SNP and small indel variant caller with deep neural networks](http://dx.doi.org/10.1101/092890)
* Ryan Poplin, Dan Newburger, Jojo Dijamco, Nam Nguyen, Dion Loy, Sam S. Gross, Cory Y. McLean, Mark A. DePristo *


TLDR: Proposed deep-learning based variant caller, "DeepVariant" outperforms current variant callers, generalizes between species and sequencing technologies. DeepVariant is based on the Convolution Neural Network InceptionV2 with pre-trained weights from an ImageNet learning task demonstrating the power of pre-training/transfer learning between vastly different domains like vision and genomics.

## Method

### Preprocessing

1. De-Brujin-graph based read alignment according to most likely derived haplotype
2. Candidate variants identified by checking if `# of reads > minimum # of reads` and `# of reads / total # of reads > thresholding fraction`

An image of size `(WIDTH, HEIGHT, 4)` is made. The `HEIGHT` is the number of reads and `WIDTH` is the length of the window. The three channels are filled as follows:

-. *Bases* channel: `{'A': 250, 'G':180, 'T':100, 'C':30}.get(base, 0)`
-. *Quality* channel
-. *Positive strand* channel: `{True:70, False: 240}`
-. *Alpha* channel


3. Randomly remove reads from loci with excessive coverage. 

#### Model

Use of convolution neural newtork "InceptionV2" with pre-initialized weights from ImageNet. Three states: `{reference, heterozygous, variant}`

Optimization:
Stochastic gradient descent with batch size 32 and RMS decay 0.9. 80 hours or converged

Hyperparameter Search:

All experiments: Grid Search of `learning_rate=[0.00095, 0.001, 0.0015]` and `momentum=[0.8, 0.85, 0.9]`

Multiple technologies experiment: `lr=0.0015` and `mom=0.8` for 250,000 steps.


### Dataset

Platinum Genomes Project, Genome in a Bottle, FDA Truth Challenge.

Genome in a Bottle used as a ground standard dataset.

### Evaluation and results

1. `Sensitivity = TP/ (TP+ FN)`
2. `PPV = TP / (TP + FP)`
3. `F1 = 2 TP / (2TP + FN + FP)`
4. Genotype Concordance = `# matching / # paired eval and truth variants`


#### Results:

1. DeepVariant is not tehcnology-specific
2. DeepVariant can generalize across species.
3. DeepVariant can generalize across genome builds.

## Future / Significance

1. Genomics is proceeding rapidly, and such automatic variant callers will help sustain the rate of progress by removing the need to manually tune the calling method.
2. Allow us to use large amounts of ground truth human data in other species.

## Thoughts

1. Generalization of DeepVariant to other species is remarkably odd. (Thought to e completed) 
3. The CNN learns the error prob distribution of multiple reads compared to the reference. This is a complex distribution and the NN demonstrates once again the representability power.
2. The use of pre-initialized Inception-v2 archietcture demonstrates the transferability of feature extraction capabilities between problem domains. (not sure if model fine-tuning was done by freezing weights or not)
