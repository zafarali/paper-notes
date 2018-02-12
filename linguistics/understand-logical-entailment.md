# [Can Neural Networks Understand Logical Entailment?](https://openreview.net/forum?id=SkZxCk-0Z)

Richard Evans, David Saxton, David Amos, Pushmeet Kohli, Edward Grefenstette (ICLR 2018)

## Background stuff:

1. The authors propose a new dataset (for entailment in propositional logic), but more importantly, a "process" through which one can ensure that there are no differences in the distribution of logical operators (disjunctions, conjunctions), # of variables or types, in the dataset that a naive model can exploit.
2. The authors use multiple NN methods (ConvNets, LSTMS, bLSTMs) to demonstrate that they do not perform too well (getting about 50-70% on their test set but all failed on their "massive test set" which contains logical definitions with 30 operators and 20 variables).
3. They show that adding inductive architectural bias about compositionality (via TreeRNNs based on the parse structure). Performance on these models is best amongst their benchmark models (getting around 60-75%, got 59% on their "massive test set")
4. Interestingly, the authors consider relational models which simultaneously work on both the premise and hypothesis (i.e. concatenate the hypothesis and premise and feed them into an (b)LSTM) but these do not perform better than the Tree structured RNNs

## Proposed model (possible worlds net):

1. The authors work on a relaxation of a "semantic notion" of entailment. so basically what the model does is that it has a bunch of vectors which it uses as a "representation of the world" in which a logical statement can be evaluated.  I think this is the kind of stuff Tim wants to head toward, semantically inspired architectures and encouraging it works really well.  It solves all test sets to near perfection > 95% and gets 73% on the "massive test set"
2. they indicate that there is no constraint on this architecture for propositional logic and can be extended to other logic including natural language sentences (which I'm assuming includes natural logic)

## Some potential issues
1. They do acknowledge that the applicability of TreeRNNs may be limited to where we have perfect parse trees.
2. Also recognizing entailment in propositional logic is much easier than in natural language sentences
