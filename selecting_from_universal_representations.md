**Selecting Relevant Features from a Universal Representation for Few-shot Classification** by Dvornik et al., March 2020

Paper link [here](https://arxiv.org/pdf/2003.09338.pdf).

## The Problem
**tldr**: They proposed SUR, a new model that replaces the common adaptation phase of meta-learning methods, 
with a simple selection mechanism over a set of universal representations.

Generally, in few-shot classification a model is explicitly trained to learn to learn via the introduction of so-called episodes.
Training in an episodic fashion is performed by setting up both training and testing environments in the following way:
- Sample a set of classes
- For each class, sample K examples
- Learn a model to ingest the K examples such that a set of new data points with the same class label are classified correctly.

This process constitutes as an "adaptation mechanism", and is often performed after a base feature-extractor network has been trained.

This paper highlights a few recent results questioning the above formulation:
- a linear classifier trained on the support sets of new classes might yield similar accuracy to this adaptation process
- a non-parametric prototypical classifier combined with a regularizer on the feature extractor might be just as good

This suggests that meta-learning may not be needed all together.

They propose SUR, "Selecting from Universal Representations", where the adaptation mechanism is replaced by a selection mechanism over
semantically different features that captures different modalities of a training set. This seems to outperform
meta-learning baselines on cross-domain tasks (such as Meta-Dataset).

## Proposed Approach
Their approach can be broken down into the following steps:
1. split class set into (i) base classes (ii) test classes, where (i) and (ii) ha no overlap. 
2. use the labeled set of (i) to learn K feature extractor function f_k ("meta"-training phase)
3. perform few-shot classification on (ii) using each class' support and query set (meta-testing phase)

The latter is performed by **Nearest Centroic Classifier** (NCC) to retrieve the final classification:

4. average each class' corresponding support examples to build a representation ("centroid") for that class
5. find nearest centroid for each query example

Each data point is mapped to a d-dimensional vector k times, where k roughly corresponds to different semantic representations of the same underlying image.
These representations need to be transformed in order to be useful in step 3. 
To do this, we learn a mask, lambda, over the kxd dimensional feature representation of some input x.
This new transformed f'(x) is then used in the NCC classifier during meta-testing.

Meta-testing is done by minimizing the negative log likelihood of the softmax of the output of the NCC 
with respect to lambda (keeping all other model parameters fixed).
Lambdas are fixed to be between 0-1, are initialized as 0s and are learned via SGD for 40 iterations.

This is a weak form of adaptation that will only change how we scale up the underlying features extracted.

Intuitively, this will ensure that lambda will be large for features where intra-class similarity is high, 
while inter-class similarity is low.

## Experiments
They experiment on mini-ImageNet (100 classes with 600 images per class) and Meta-Dataset (multiple-datasets combined).
In the former case, they perform fixed K-shot learning with K=5. 
In the latter's case, they follow the Meta-Dataset original setup, and allow K to differ.

Their observations are the following:
- training on ImageNet only yields the best results on 8/13 datasets on the single-domain tasks (w/o SUR)
- SUR outperforms 3 baselines: 1. pretrained single feature extractor on ImageNet, 2. pretrained single feature extractor on 8 datasets from Meta-Dataset ,
3. multiple feature extractors with lambda=1 for all features
- features selected by SUR are sparse, and correspond to feature extractors that come from visually similar domains.

## Personal notes
- as they also point out, one has to be careful with interpreting the SUR numbers in table 2. as this model uses 8 x ResNet18 number of parameters.
I think SUR-pf is much more of the direction that will be useful for people, as training 8 ResNet models may be too expensive (time/maintainance/computational resources).
- otherwise, this paper has some nice insights that may change how much "meta"-training there will be in future papers.
