**Entities as Experts: Sparse Memory Access with Entity Supervision** by Fevry et al., 2020 (Google Research)

Paper can be found [here](https://arxiv.org/pdf/2004.07202.pdf).

## The Problem
There has been some recent work on how pretrained Transformer models can act like knowledge bases.
The way these models can retrieve information that some would call "factual" is by simply memorizing information from the training data in the model's parameters.
Though I'd argue that it may be problematic to call something factual based on this retrieval paradigm, this paper focuses on enabling Transformers to access
a memory storage about entities that capture information that models learned during the trining process.

The authors claim that this extention to Transformers makes it easier to perform well on Trivia QA tasks, while also allowing the model to only access parts of the model parameters
that explicitly refer to this entity memory storage at inference.

## Proposed Approach
They propose an extension to the Transformer architecture that includes an external memory storage for entity mentions.
These entities are pre-defined, and their representations are captured during training the Transformer model from raw text.

## Experiments

## Peronal notes
- their method requires a fixed set of entities
- not sure how this model is more interpretable than Transformer, we are still storing the model's beliefs as an entity embedding matrix
