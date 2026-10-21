---
title: "CRISPR Got the Nobel. The Guide RNA Is the Part I Keep Thinking About"
date: 2020-10-07 09:00:00 -0400
categories: [Science, Biology]
tags: [crispr, gene-editing, nobel-prize, biotechnology, chemistry]
description: "CRISPR/Cas9 wins the 2020 Chemistry Nobel. A few thoughts on guide RNA, what makes the tool useful, and why cutting DNA is only part of an edit."
media_subpath: /assets/img/posts/2020-10-07-crispr-cas9-programmable-genome-editing/
image:
  path: cover.png
  alt: "Abstract CRISPR guide RNA aligning Cas9 cutting geometry with a DNA strand"
math: false
mermaid: false
---

Emmanuelle Charpentier and Jennifer Doudna have won this year's [Chemistry Nobel for genome editing](https://www.nobelprize.org/prizes/chemistry/2020/press-release/). CRISPR/Cas9 gets described as genetic scissors a lot. Fair enough, but the scissors aren't the part I find most interesting.

It's that you can change where they go by changing an RNA sequence.

That sounds like a small distinction until you think about having to build a new tool every time you want to target a different bit of DNA. With this system, much of the machinery stays the same. The guide changes. As someone interested in both biology and software, that's the part that grabs me.

## What the guide actually does

Cas9 is an enzyme that can cut DNA. The guide RNA helps it recognize a target through matching bases. There are constraints, including a nearby sequence called a PAM, so this isn't an unrestricted find-and-replace command.

Charpentier and Doudna's work helped turn a bacterial defense system into something researchers could redirect. Their 2012 work showed how the RNA components could be combined into a single guide and used to direct DNA cutting in a test tube. The Nobel's [scientific background](https://www.nobelprize.org/prizes/chemistry/2020/advanced-information/) is worth reading for how that became a genome-editing tool.

The cut is only one step. A cell still has to repair the DNA. That repair can disrupt a gene, or under suitable conditions use a supplied template to make a particular change. Choosing a target doesn't guarantee that every cell ends up with the edit you wanted.

This is where the software analogy starts giving me trouble. You don't get to inspect a diff, hit save, and assume the rest of the cell is fine.

## The research uses are exciting enough

The medical possibilities get most of the attention, understandably. But making it easier to test what a gene does is already a huge deal. Change it, look for a difference, then work out whether that difference really came from the change.

The last part still needs controls and follow-up experiments. An unexpected result could be interesting biology, an unwanted edit, or a problem with the experiment. A convenient way to cut DNA doesn't settle that for you.

I'm excited by the number of questions this makes easier to ask. I'm less comfortable with the jump from "we can target this sequence" to "we can fix this disease." Delivery, unwanted changes, and the behavior of the edited cells are separate problems. Heritable edits also bring questions that a better enzyme cannot answer.

Still, being able to redirect a molecular tool by changing its guide is pretty wild. That idea is enough to keep me interested without pretending the rest of biology has become easy.

## Sources

- Nobel Prize, [2020 Chemistry announcement](https://www.nobelprize.org/prizes/chemistry/2020/press-release/), October 7, 2020.
- Nobel Prize, [Scientific background: A tool for genome editing](https://www.nobelprize.org/prizes/chemistry/2020/advanced-information/), October 7, 2020.
