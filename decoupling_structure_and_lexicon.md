**Decoupling Structure and Lexicon for Zero-Shot Semantic Parsing** by Herzig and Berant, 2018.

Paper can be found [here](https://arxiv.org/pdf/1804.07918.pdf).

## The Problem
**tldr:** This paper proposes a new method to perform semantic parsing on new target domains 
where entities and relations have not been observed previously (hence zero-shot).

Semantic parsing concerns the problem of mapping natural language to executable logical form (e.g SQL). 
These systems form the backbone of conversational interfaces, hence are of great interest.

Previous approaches have used weak-supervision, human-in-the-loop and paraphrasing to collect data on new target domains.
In comparison, here they try to leverage data from previously seen domains to generalize to a new target domain.

## The task
Semantic parsing is typically performed via encoder-decoder style (seq-to-seq) models with attention, where the input is a 
question in natural language and the output is the logical form representation of the question.
To perform this task over multiple domains, people have used a single seq2seq model trained on all domains with shared parameters.
Though this approach requires annotations on the target domain (?), 
here they solve this task without any examples from the target domain.

## Proposed Approach
To do this they introduce the following steps:
1. Delexicalize tokens in the target domain
2. Map each to a domain-independent representation
3. Map each representation to an abstract logical form that contains slots instead of KG constants
4. Learns an alignment model to map each slot with a KB constant


This is achieved by two models: 
- The Aligner: an unsupervised model that learns alignment between slots of the logical form
(the output sequence) and KB constants in the input sequence. 

- The Structure Learner: learns seq2seq model to map delaxicalized input to abstract logical form.

First they perform delexicalization by following a rule-based method. This will convert both KB constant and other content words
to generic abstract types based on a set of categories (e.g noun, verb, adjective, date, entity etc.).
They keep domain-general words such as "be", "what", "many" etc. as is as these are shared across domains.
They assume to have access to a lexicon that converts KB constants to natural language.

Next, we train the Structure Learner seq2seq model to map this abstract representation of the input sentence to the abstract logical form of the question.
Note, the output here contains generic slots without concrete KB constants.

To assign each slot to a KB constant, we use the alignment model. This alignment model tries to learn alignment scores between lexical 
utterences and tokens in the logical form, i.e. P(x_abs|z_abs). Since this model is unsupervised as there is no gold alignments available,
they generate a gold alignment matrix A* by training a separate supervised model that mimicks the output of the former model.

Since the Structure Learner already learnt an abstract logical form of the input question, we can use this as labels in a supervised model.
For this model, they use two BiLSTM to encoder both the input question (in original form) and the abstract logical form.
They then take the hidden states of each encoded input and model the alignment probability P(x_abs|z_abs) via
a bi-linear form followed by a softmax layer. They train their model by minimizing cross-entropy for words in z_abs that correspond to slots.

## Experiments

Evauation is done on the OVERNIGHT dataset. This contains 13,682 language utterence <> logical form pairs across diverse domains.
Their approach assumed linguistic regularities across the individual domains, i.e. if a domain contains concepts that are not present in the other domains,
we cannot perform zero-shot generalization.

They used accuracy as a metric where predicted logical form has to exactly match the target labels.

