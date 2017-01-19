# [Towards Information Seeking Agents](https://arxiv.org/pdf/1612.02605v1.pdf)
* Philip Bachman, Alessandro Sordoni, Adam Trischler *


TLDR: Deep Networks are used to learn policies that allow agents to query environments and make decisions in a step towards generalized artifical intelligence. Objectives are formulated on reward functions that result in models learning to ask better questions.

## Method

* Attention as a strategy: * Information seeking is related to attention. That is where to focus to ask new questions to improve an internal representation can be connected to attention. By virtue of attention, models can also prevent focusing on irrelevant features. 

The key contribution here is the objective and how to think about it.

### Objective

Formulated as a sequential decision making problem where at each step, we have all the information collected so far to ask the next best question. 

* Information-seeking objective:  Encourage questions that are most likely to reduce error in model predictions. 

Overall objective:

`max_theta E_D [ E_questions [ sum_{t=1,..,T} R_t(f_theta, x, y )] ]`

Basically, we are maximizing over the parameters (policy) that can ask questions about a datapoint in `(x,y)~D` that can maximize a reward function that is based on the belief of the world `f_theta`.

* Example: `(x,y)` is an image and annotation. A possible question `q_t` could be a small window from `x`. The observation function `O(x, q_t)` will return the value of those pixels. `R_t` is log-likelihood that `f_theta` assigns to the true value of `y` once it has observed the answers to questions `q_1, ... , q_t`.

### Training

The derivative of the objective can be separated into two:

1. Wrto the policy: this modifies the question and answer distribution
2. Wrto the belief (`f`): modifies 

Train gradient wrto beleif using backpropagation and MC integration.

Train gradient wrto policy using Generalized Advantage Estimation (GAE) which trains model to make better decisions.

#### Reward Function 

Separate reward function into two parts: extrinsic and intrinsic.

* Extrinsic:  incorporation of external feedback eg: performance metrics, cross entropy functions.
* Intrinsic:  task-agnostic metric that drives curiosity to ask better questions. Authors use cross-entropy using q(x|f_theta) which encorages model to form a better belief about D.

(!) Each question is rewarded by the difference between the reward from all previously asked questions and the reward from then asking the new quesstion `q_t`. This favours questions that exploit more information from the world.

### Architecture

The policy `pi`, belief `f` and value estimates `V` are based on deep neural networks with shared parameters. The inputs are a *table* tuples of `(qrepr, qansr)`. `qrepr` represents which question was asked, `qansr` represents the answer. The neural networks take in all questions observed so far and feed them through the hidden layers. 

* image analysis: they use two networks (bottom-up and top-down). The **bottom-up** uses conv nets for sumarize a **masked** image into `V` and the label `f_y`. For last layer they use LSTM which stores the internal representation between steps. The **Top-down** network takes input the last layer of the bottom-up and does *layer integration* between the two networks. The output of the top-down network is the reconstructed image `f_x` and a policy `pi`. Iterestingly, this network simulatenously learns to query and reconstruct the image.

* general architecture:  input is now an masked observation vector and a mask vector indicating features requested by model. All outputs `f_x,f_y, V, pi` are computed from the final layer of the network.

* other information
- ADAM Optimizer
- Batch norm
- Leaky Relu
- minbatch 100


## Evaluation and results

### Cluttered MNIST

Dataset: small MNIST digit in a big picture with some clutter.

Model learns to detect a digit from a masked image and then concentrate efforts in that region. Sometimes did not find a digit before time ran out (41 queries were allowed.) Model had 4.5% test error which was better than 8.1% from the RAM model but slightly worse than the 3.36% from the DRAW model.

### BlockWorld

Dataset: synthetic, 3 colored objects in an image. Question is about two objects and their relative positions eg: (Yellow triangle, above, yellow square).

Input is an image 10x16x16 containing the image, statement and the true label summarized.

Model seems to learn how to look for the answer, in Fig 3. First exploring the image for the objects and then eventually realizing that the answer to the question is false. Learned policy reaches accuracy of about 98%. A random policy reaches 71%.


### CelebA

Dataset: celebrity images with annotation related to their attributes (gray hair, bald, etc)

The model is able to make quite accurate decisions (85.9%) after querying just 5 questions. However, it performs only ~10% better than a random policy. After 20 questions, the model gets an accuracy of 87% which reaches the limit of using unmasked images (88.3%).

### Hangman

Dataset: 16-character sequences from Text8 corpus. Reinforcement learning using the reward of +1 for correct guess and -1 for incorrect guess.

Model performs better than random and frequency distribution policies. Also performance seems to saturate after 100K iterations.


## Thoughts

1. Brilliant computational view of the "Information Seeking" game in that the optimal question at every turn is based on the optimal questions asked at the previous turn.
2. Lots of arbitriary downsampling and upsampling. Could there be a better method of summarizing this input data?
3. Very cool visualizations of how an image is being searched for the right answer. 
4. Coming back to the "20 questions" task, is there a dataset which contains (question, answer) attributes for a famous person and the label where the model has to guess who the person is? How would this data be stored? Very interesting to think about. I imagine there are some datasets with annotated objects in images where one can ask "is there a tree" etc etc, could we train a model to ask human interprable questions?