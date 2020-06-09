**Meta-Dataset: A Dataset of Datasets for Learning to Learn From Few Examples**, by Triantafillou et al.

Paper link [here](https://arxiv.org/pdf/1903.03096.pdf).

## The Problem
**tldr**: Meta-Dataset is a new dataset that contains a diverse set of previous few-shot classification benchmark tasks (for vision) where few-shot learning refers to a model's ability to learn new classes only from few examples.

This paper addresses the limitation that two commonly used few-shot learning benchmarks, Omniglot (hand-written digit classification) and mini-Imagenet, seem to saturate in terms of distinguishing between different approaches. Specifically, they highlight:
1. task homogeneity (e.g. same domain + imbalance in how many examples are seen per class <-> fixed K-shot learning)
2. within-dataset generalization 
3. ignore relationships between classes when forming episodes

## Proposed Approach
They propose Meta-Dataset that is:
- much larger scale (contains 10 datasets with different visual concepts and different "fine-grainedness")
- has diverse data distributions
- task creation is based on class structure
- uses realistic class imbalance
- varies the number of classes per task and size of training set -> measures robustness of models across different shots

### Dataset
Meta-Dataset contains:
- ILSVRC-2012/ImageNet
- Omniglot
- Aircraft
- CUB-200-2011
- Describable Textures
- Quick Draw
- Fungi
- VGG Flower
- Traffic Signs (reserved for evaluation only)
- MSCOCO (reserved for evaluation only)

### Class structure
Ontologies are available for ImageNet and OmniGlot. For ImageNet, they take 1000 classes (leaf nodes) and split it into three subgraphs for training, validation and testing. This ensures no semantic overlap between training/validation/testing.
For Omniglot, they use a two-layer class hierarchy of alphabets and characters.

### Episode Creating
An episode describes a task in which the model has to learn N new classes from K examples from each class. Episodic training then refers to the process of doing this repeatedly over different N-way K-shot tasks. 

In Meta-Dataset they construct an episode as follows:
1. Uniformly sample a dataset, D
2. Sample a set of classes C from D, and assign to train/val/test splits
3. Sample support and query examples for each class in C

**Sampling Classes:** For ImageNet they sample a non-leaf node, and take the leaf nodes spanned by the given node. The larger the height of non-leaf node and the leaf node, the more coarse-grained the classification becomes.
For Omniglot, they uniformly sample an alphabet for each train/val/test split. Then they uniformly sample "way" number of characters as classes. This ensures that each episode contains examples from the same alphabet.

**Sampling examples:** The query set if balanced (capped at 10), ensuring that they test equally on each class. Whereas the support set if imbalanced, allowing it to be more aligned with real-world imbalances across different classes. Support sets are constructred such that they exclude any image from the query set, and are capped at at most 100 per class. The total support set is capped at 500. Additionally, they randomly sample an integer from the (0, 1] interval and multiply the # of examples for a given class to yield the number of examples that can contribute to the support set. This is done such that they can study the sensitivity of how "few" examples the model has to learn from.

## Experiments
**Non-episodic baselines:**  
- k-NN: train backbone network on N classes, and find nearest neighbor of the query example from the set of support set examples.
- Finetune: finetune backbone network on new classes' support set examples by adding a small network on top of the backbone network.

**Episodic baselines:**
- Protypical Networks, Matching Networks, MAML, Relation Networks, Proto-MAML

**Observations**
They compare two settings: training on ImageNet only versus training on all datasets. Not so surpisingly, training on ImageNet only yields good enough results for datasets that visually resemble ImageNet. For Omniglot, Aircraft, and Quick Draw they observe a benefit from training on all datasets.

Ways and shot analysis:
Generally, the more classes a model has to learn the less accurate it is. Moreover, the more examples the model has per class, the more accurate it is. However, models tend to have different rates to which they utilize more or less examples: e.g. Proto-MAMl and Prototypical Nets perform best in the low-shot setting, but saturates in the higher shot settings.

Effect of pre-training meta-learners:
They analyze the effect of using the pretrained weights from the backbone of k-NN (trained on ImageNet or trained on all datasets) on meta-learners.
Pre-training seems to be helpful, but the effect changes with how visually similar each dataset is to the pretrained weights' underlying data (e.g. Omniglot and Quick Draw).

#Effect of episodic testing without training
They evaluate settings in which they remove meta-training from the training process, but evaluate model in a "meta" fashion (with the episodic setup). Surprisingly, they find very competitive results between meta and non-meta settings, suggesting that explicit meta-training may not always be needed to still perform meta-learning at testing.

## Personal notes
- important contribution to the meta-learning literature
- finally a paper that changes the problem formulation to allow K to vary in K-shot learning (!!)
- analysis on the effect of non-episodic training is very interesting, and has a lot of implications for the design of meta-learning algorithms in the future
- would be useful to see if the same conclusions can be made for non-image few-shot datasets
