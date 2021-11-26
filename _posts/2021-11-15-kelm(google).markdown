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

