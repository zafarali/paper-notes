# [Pixel Recurrent Neural Networks](https://arxiv.org/abs/1601.06759)
* Aaron van den Oord, Nal Kalchbrenner, Koray Kavukcuoglu *


TLDR: Sequential, pixel-by-pixel prediction using Recurrent Neural Networks

## Importance

1. Estimating distributions of natural images is a difficult problem. However, the reward is high because it can be used for generation of new images. Conditional distributions can be used to predict next frames in video or even text-to-image synthesis.


## Method

1. Joint distribution over pixels is to expand as a product of conditionals of sequential pixels. ( `P(x) = prod_{i=1,n^2} p(x_i | x_1, ..., x_n )` )
2. RNNs are naturally good at modelling sequential prediction problems. 2D-RNNs have been previously used for modelling. Authors take that futher.
3. Model pixels as discrete x_i ~ Multinomial rather than continous * (<-- interesting, should explore further) *



### Architecture

General idea is

0. Scan pixel by pixel of an image, along with some context about the other pixels in the image.
1. for each pixel `x_i`: compute `P(x_i | x_j, 0=<j<i)`
2. Also then factor out the 3 RGB chanels.
3. LSTM can handle the long range interactions between `x_i`'s that need to be modelled.

#### Row LSTM

1. Scan image row by row, and then pixel by pixel. * (not 100% how we get a triangular context here...) *

(refer to equations in paper for actual calculations, note that equations are not matmuls but convolutions)

#### Diagonal BiLSTM

Good of parallelizing and for capturing more than a triangular context. Basically take the Row LSTM and use it in the other direction so we can cover the whole image. Images are skewed by adding one position to each previous row. This is done so that convolutions can be applied on the diagonal. The authors use "residual connections" where the functions represented by the layers are only learning the differences (i.e the residuals - see original paper on residual learning).

To ensure that when predicting the R channel, the B and G channel is not seen and when predicting the B channel only the R channel is seen, masked convolution is used. Masking ensures that predictions are made based only on pixels that were already predicted in the past. Implementation: zero some of the weights after every update.

#### PixelCNN

Problem with LSTM layers: unbounded dependency range.
Solution: Use convolutions to compute fixed dependency range.
This is only used when training/testing but not when generative, as during generation you anyways have to predict the pixels sequentially. 

#### Multi-Scale PixelRNN

First network is an "unconditional" PixelRNN that generates a subsampled image. Then a "Conditional" PixelRNN will take this and generate the larger image. 

* Conditional PixelRNN: * `conv` layers and followed by a `deconv` layer to create a feature map (needs more reading to understand)

## Evaluation and results

### Normalized log likelihood

Normalized Log Likelihood is obtained for the data under the model. To ensure comparability with the literature, they sample x_k ~ Unif(i,i+1) where i is in [1, 256]. For example if x_k was predicted to be 158, x_k is set to be to some draw from U(158, 159). Normalize by the dimensionality of the image. 


Interpretation: "number of bits that a compression scheme based on the model would need to compress every RGB color value".

### Results:

1. Skip connections and residuals increase performance on CIFAR.
2. So does increasing the number layers. 
3. Diagonal BiLSTM has best performance because of global view (compared to local view of Row LSTM and PixelCNN)

(Questions: is the difference between 3.08 and 3.06 represent a model that much better?)

Multiscale model is able to capture the global dependencies of the image (not completely convinced...) they have the same LL but there is a qualitative difference.

## Thoughts

1. Interesting use of the softmax function to predict a disrete value for the images. 
2. The concept of using a pixel by pixel prediction sounds like a promising concept to take to other domains, maybe even in the bidirectional case.

