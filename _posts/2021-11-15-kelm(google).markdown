---
title: KELM(Google)
categories: nlp
tags: 
    - nlp
    - knowledge graph
    - knowledge enhancement
    - papers
author: Zhou Tong
sidebar:
  nav: Paper-notes
  avatar: true
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
    src: /pictures/bg1.jpg
---

<!--more-->

See [Original Paper](http://arxiv.org/abs/2010.12688) for more details.

In contrast to the many architectures that have been developed to integrate these two sources, our approach converts the KG into natural text, allowing it to be seamlessly integrated into existing language models.

It carries the further advantages of improved factual accuracy and reduced toxicity in the resulting language model. We evaluate this approach by augmenting the retrieval corpus in a retrieval language model and showing significant improvements on the knowledge intensive tasks of open domain QA and the LAMA knowledge probe.

We convert the English Wikidata KG into natural language text. The generated corpus is called KELM Corpus.
- 18M sentences spanning
- 45 triples
- 1500 distinct relations

For training the verbalization system, we also create an English Wikidata KG-Wikipedia Text aligned corpus consisting of a variety of entities such as dates and numerical quantities.

Facts may not be expressed as explicitly in text as they are in KGs, and the variability in the quality of text can eventually cause biases in the resulting models.

## TEKGEN

TEKGEN (Text from KG Generator): A data-to-text sequence-to-sequence model for verbalizing an entire KG.

One of the challenges in converting an entire KG to text is the wide variety of entities and relations.

1. KG triples are aligned with Wikipedia text using distant supervision.
2. T5 is fine-tuned sequentially first on this corpus, followed by a small number of steps on the WebNLG corpus.
3. BERT is fine-tuned to generate a semantic quality score for generated sentences w.r.t. triples.
4. To generate the KELM corpus, entity subgraphs are created using the relation pair alignment counts rom the training corpus generated in step 1. The subgraph triples are then converted into natural text using TEKGEN.

![tekgen pipeline](/images/tekgen-pipeline.jpg)

### KG-Text Alignment

```python
alignment_pairs = set()
for t in sentences:
  # sentences are all sentences from root section of Wiki page of entity s
  for triple in triples:
    # triple = (s, r, o)
    if t.contains(alias(triple[2])]):
      if t.notcontains(alias(triple[0])]):
        p = t.first_pronoun
        t.replace(p, name(triple[0]))
      alignment_pairs.add((t, triple))
```

For each entity, we constrain the candidate sentences to the root section of its Wiki page because this section generally describes the relations of the subject entity with other entities.

If any alias of the subject entity occurs in the given sentence, the sentence is selected as is, else the first animate third-person personal or possessive pronoun is replaced by the subject entity's canonical(????????????) name.
The pronoun replacement heuristic(????????????) also works well because of the constraint. All triples aligned to a given sentence are combined together as a single example.

### Types of Triples

pass

### Model

A two-step sequential finetuning of the pre-trained T5-large model for converting triples to text.

> Triples: (subject relation_1 object_1,....relation_n object_n) as input to T5

First, T5 model is fine-tuned on the aligned corpus for 5k steps to increase the coverage of entities and relations.
But it has a problem that when the triple is missing a certain expected input, the model will hallucinate a random one. For example, ???Neff Maiava date of birth 01 May 1924, date of death, 21 April 2018.??? generates ???Neff Maiava (1 May 1924 - 21 April 2018) was an Albanian actor.???; hallucinating a profession.

To overcome this problem, the model is further fine-tuned on WebNLG 2017 data for 500 steps. While WebNLG has low coverage, the information in the input triples matches the target sentence exactly.
WebNLG also has a different sentence structure than Wikipedia. This reduces conformity(?????????) to Wikipedia sentence structure and hence reduces hallucination.

> learning rate: 1e-3
> batch size: 2^20 = 1048576
> max decoding length: 256

### Quality Filtering

A semantic quality based filtering. It is not jointly optimized with the text generation module.

A semantic quality score is assigned to each generated sentence to show whether it captures the full meaning of the triple and does not hallucinate extra information.
The score is generated using a BERT base uncased model with input of the form [CLS] concatenated-triples [SEP] reference-or-generated sentence.
It is fine-tuned for 1k steps on WebNLG 2017 human assessment data.

## KELM Corpus

Utilize the TEKGEN model and filtering mechanism to build a synthetic corpus that captures the KG in natural language format.

### Entity Subgraph

Using relation co-occurrence counts based on realtion-sentence alignment in the training data.

### Generation

For each entity subgraph, we concatenate all its triples as before. We perform top 5 sampling with a temperature of 0.5. The bottom 1% of the generated sentences are filtered out based on the semantic score.

### Human Evaluation

The generated text is rated for two aspects--fluency and semantics, on a scale of 1-5, where 1 means not fluent/does not capture meaning at all and 5 means completely fluent/fully captures meaning with no hallucination. For each instance, scores of the two annotators are averaged to get the final rating.
