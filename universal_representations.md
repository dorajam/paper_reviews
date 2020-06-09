**Universal representations:
The missing link between faces, text, planktons, and cat breeds**, by Bilan and Vedaldi, 2017.

Paper link [here](https://arxiv.org/pdf/1701.07275.pdf).

## The problem
**tldr:** This paper investigates how well a single neural network can extract so-called "universal" representations,
 i.e. representations that are robust across visually diverse sets of images.
 
In particular, they investigate how model capacity relates to the complexity of the dataset (complexity as measured by the number of combinations of visual concepts).
They argue that children learn internal representations of visual concepts early in life, that only changes sligthly in later years.
This implies that there may be a general-purpose representation that could be learned once for all.

They investigate this question via the question of model capacity and diversity of tasks.
If tasks are completely independent, without any visual overlap, the total size of a model should grow proportionally.
On the other hand, if tasks overlap, then the complexity growth gradually slows down, allowing model complexity to catch up,
so that, in the limit, universal representations become possible.

## Experiments

Three sharing setups:
1. no sharing: train seperate network on each dataset
2. full sharing: train single model on 3 datasets where each performs 10-way classification (i.e. same neuron corresponds to an mnist digit or CIFAR object)
3. deep sharing: train single model on all but last layer, where last layer will be fine-tuned on each dataset. 
4. partial sharing: make first or last block of ResNet be task-specific, and share the rest of the network across datasets.

They find very similar performance from 1. and 2.. 
Surprisingly they find that 3. has the best performance, however this includes domain-specific batch normalization, which seems to have great importance.

They show that certain networks can in fact perform as well, and better than networks specialized in a single visual domain.
However, these require to carefully normalize the information in the network (e.g. domain specific scaling factors, instance normalization etc.), 
in order to compensate for inter-domain statistical shifts.
