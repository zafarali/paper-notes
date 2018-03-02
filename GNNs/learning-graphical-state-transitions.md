# [Learning Graphical State Transitions](https://openreview.net/forum?id=HJ0NvFzxl&noteId=HJ0NvFzxl)
Daniel D. Johnson, ICLR 2017


## Key Ideas:
- Hidden states are graphs trained with _strong_ supervision
- This allows the hidden states to be interpretable. Somewhat restrictve but could be useful in fields like genetics and linguistics


### Definte the notion of a "soft graph"

- Nodes in the graph "exist" with strength s \in [0, 1]
- Each node has a distribution over types.
- Edges have a type as well
- Nodes have a hidden state based on the propagation mechanism

### Transformation operations

These operations act to take in a graph and some external information and output a new graph

- Add node
- State update
- Edge update: use new info to make edge type updates and connections
- Propagate: message pass between states
- Aggregate: use state information and convert it into an output

## bAbI task

- training with text and input
- use true graph as strong supervision for the hidden state (during training)
- replace fuzzy graph with true graph (during training)
- Seems like the graph model works quite well on all BABI taks
- Learns even the graph-specific task that memory networks fail at
- Learns meaningful internal graphs

## Questions
1. How can we use this?
2. What kind of tasks are suitable for this kind of work?
3. How can we reduce the need for strong supervision?
4. Mixing strong and weak supervision?
5. Suboptimal graphs?
5. Can we generalize between tasks in a few-shot learning sense? i.e. is the hidden state general?
6. Example strong supervision on one task, no supervision on another




### Background and Extras

1. [Slides](https://docs.google.com/presentation/d/1NswYuIOXYavLrN0R02Kc0HUyH66rFH5wXVL-9rwkGYM/edit#slide=id.gc6f9e470d_0_0)
4. [Paper](https://openreview.net/forum?id=HJ0NvFzxl&noteId=HJ0NvFzxl)
5. [Blog](http://www.hexahedria.com/2016/11/06/introducing-the-ggt-nn.html)
6. [Reviews](https://openreview.net/forum?id=HJ0NvFzxl&noteId=HJ0NvFzxl)
7. [Code](https://github.com/hexahedria/gated-graph-transformer-network)

#### Background on GNNs

1. [Scarselli, Franco, et al. "The graph neural network model." IEEE Transactions on Neural Networks20.1 (2009): 61-80.](http://ieeexplore.ieee.org/abstract/document/4700287/)
2. [Li, Yujia, et al. "Gated graph sequence neural networks." ICLR 2016 (2016).](https://arxiv.org/abs/1511.05493)
3. [Weston, Jason, et al. "Towards ai-complete question answering: A set of prerequisite toy tasks." arXiv preprint arXiv:1502.05698 (2015).](https://arxiv.org/abs/1502.05698)
