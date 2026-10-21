---
title: "BERT: Let the Model Read Both Sides"
date: 2018-10-11 09:00:00 -0400
categories: [AI, Software]
tags: [bert, natural-language-processing, transformers, machine-learning, pretraining]
description: "Notes on Google's BERT paper: learning from both sides of a sentence, reusing a pretrained model, and what I'd want to check beyond the benchmark scores."
media_subpath: /assets/img/posts/2018-10-11-bert-bidirectional-language-pretraining/
image:
  path: cover.png
  alt: "Abstract masked tokens flowing into a bidirectional Transformer encoder"
math: false
mermaid: false
---

The idea in [Google's new BERT paper](https://arxiv.org/abs/1810.04805v1) sounds obvious at first: if a model is trying to understand a word, let it use the words on both sides.

For predicting the next word, left-to-right makes sense. You can't use the rest of a sentence that hasn't been written yet. But if you're answering a question about a paragraph that's already there, why throw away half the context?

## Give it a blank to fill in

BERT uses a Transformer encoder and learns to predict selected tokens from their surroundings. Most selected tokens are replaced with a mask; some are replaced with a random token or left unchanged. That training setup lets the encoder use context from both directions throughout its layers.

The paper also trains it to distinguish real consecutive text segments from randomly paired ones. Then, for a task such as question answering, you add an output layer and fine-tune the model, including its pretrained parameters. You're not just training a small classifier on top of a permanently frozen encoder.

Reusing that starting point is the appealing part. I'd much rather spend time figuring out whether my labels make sense than build a new architecture for every text problem. Of course, a reusable model doesn't make bad labels any less bad.

## Context was already the point

BERT isn't the first attempt at this broader idea. [ELMo](https://arxiv.org/abs/1802.05365) already gives words representations that depend on their context. [OpenAI's GPT](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf) pretrains a Transformer and adapts it to other tasks. What's interesting here is how BERT makes joint left-and-right context part of pretraining a deep Transformer encoder.

I keep coming back to ordinary ambiguous words. "Bank" in a sentence about a river should be treated differently from "bank" in a sentence about a loan. The words that resolve that ambiguity don't politely stay on the left. That's a simple example, but it makes the design choice feel less abstract to me.

The reported results cover eleven NLP tasks. That's encouraging, though I don't want to read a leaderboard as proof that the model understands language the way a person does. I'd want to see what it does with awkward wording, unfamiliar subject matter, and a small dataset whose labels aren't especially clean.

There's also the practical question of cost. Reusing pretrained weights sounds great; producing them yourself is another matter. The paper says code and pretrained models are planned for release by the end of October. For now, this is a paper to follow, not a setup I've tested.

I'm curious whether using one starting model will actually make these projects less fiddly. That's the promise I'd care about most when trying it on a real dataset.

## Sources

- Devlin et al., [BERT, original October 11 preprint](https://arxiv.org/abs/1810.04805v1), 2018.
- Peters et al., [Deep contextualized word representations](https://arxiv.org/abs/1802.05365), 2018.
- Radford et al., [Improving language understanding by generative pre-training](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf), 2018.
