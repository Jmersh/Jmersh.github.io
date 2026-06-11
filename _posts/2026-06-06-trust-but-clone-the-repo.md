---
title: "Trust, but Clone the Repo: Fact-Checking AI-Assisted Academic Papers"
date: 2026-06-06 10:00:00 -0400
categories: [AI, Research]
tags: [ai, research-integrity, reproducibility, arxiv, machine-learning, academic-publishing]
description: "I audited three AI-assisted academic papers against their own code, data, and citations. The polish held up; the numbers did not."
media_subpath: /assets/img/posts/2026-06-06-trust-but-clone-the-repo/
image:
  path: cover.png
  alt: "Repository, paper, and verification checks connected in an audit workflow"
math: false
mermaid: false
---

In May 2026, [arXiv announced](https://www.nature.com/articles/d41586-026-01595-5) a one-year submission ban for authors whose papers show clear signs of unchecked AI output, such as hallucinated references or chatbot instructions left in the text. After the year, they have to clear peer review before posting again. The arXiv computer science chair, Thomas Dietterich, framed it as a response to volume: submissions have climbed sharply since ChatGPT, and desk rejections with them. The machine that writes plausible academic prose has met the machine meant to check it, and the checking is losing.

The ban aims at the obvious tells. The failure upstream of them is quieter: papers that look finished but were never checked by a human against their own data. I went looking for it with a simple test. Take recent papers that link to their own code or data, clone the repository, and see whether the published claims survive contact with the files. One author's work turned out to be a clean place to run that test, so I pulled apart three of his papers, down to the data, the code, the numbers, and the citations. The question is narrow, and it is not about the person: does the published work hold up when you check it, and can you check it yourself.

A note on confidence. Everything I assert below sits in one of three buckets. **Verified firsthand** means I pulled the primary artifact myself (code, data, or PDF), and you can rerun the check. **Unverified** means the evidence I have does not settle the question, so it stays open. **Falsified** means a paper's own claim failed the check. The buckets stay separate because a verified fact lends unearned credibility to whatever sits next to it, and this piece names real people; there is a status table at the end, and a disclaimer about what my findings do and do not establish.

![Repository, paper, and verification checks connected in an audit workflow](preview.svg){: w="700" h="394" .shadow }
_Polished prose is cheap. Reproducible verification still has to touch the files._

## Case One: A Paper Whose Data Does Not Match Its Own Description

Start with what anyone can pull. Everything in this section I checked myself, and you can rerun all of it.

In February 2026 The Geographical Journal published "Computational Analysis of Contested Monuments and Collective Memory in a Multiethnic City" (Nicula, Cretan, Dragan, and Oancea; DOI [10.1111/geoj.70073](https://doi.org/10.1111/geoj.70073)). It runs a computational sentiment pipeline over Romanian news coverage of four monuments in Cluj-Napoca and concludes that public discourse around them grew more negative and intense over the 2022 to 2025 window. The paper's data availability statement points to a public repository, [github.com/bogdanoancea/sentometric](https://github.com/bogdanoancea/sentometric). I cloned it. It holds the analysis script and the dataset in a single commit dated 21 February 2026, the day after publication. Here is what those files show.

**The corpus is 61 articles, and the trend comes from how it was assembled, not from the world.** The paper's data section says the media collection covers "the period January 1990 to November 2025," a span of nearly 36 years. The repository dataset does not. Laid out plainly:

- Stated window: January 1990 to November 2025.
- Actual data: July 2011 to January 2025.
- Empty stretches: nothing before 2011, and nothing at all in 2017, 2018, 2019, or 2020.
- Concentration: 21 of the 61 articles, a third of the corpus, are from 2023 alone.
- Per monument: Scoala Ardeleana 20, Matthias Corvinus 15, Baba Novac 14, Avram Iancu 9, plus three rows with no date or monument filled in.

So the stated window is wrong at both ends: the corpus starts about 21 years after the claimed start and ends about 10 months before the claimed finish. When a third of the evidence is from one recent year and most of the stated window is empty, a finding that discourse "intensified" in recent years is hard to separate from the obvious fact that recent online articles are easier to find. The trend is a property of the sample, not a discovery about Cluj-Napoca.

**The scoring instrument is tuned, by default, toward the result the paper reports.** I read the script's defaults: five separate mechanisms push every score downward before a single result is read. Positive sentiment is multiplied by 0.60 and negative by 1.15, so positives shrink and negatives grow; the code comment calls this "shrink positives, slightly amplify negatives." The neutral band is lopsided, requiring a score to clear +0.10 to count as positive but only -0.03 to count as negative. Two separate "vandalism" rules subtract a fixed penalty from any trigger-word article not already scoring clearly below zero. And a "domain prior," on by default, takes any article with a hostile or destructive event code and flips its label while subtracting a further penalty.

To its credit, the paper discloses the tilt. The methods section says it "applied an asymmetric scaling transformation that attenuates positive scores and amplifies negative ones," that "negative classification required weaker evidence than positive classification," and that destructive events "could not be assigned a strongly positive sentiment." The trouble is that the stated justification is close to circular: the instrument is tuned to read negative because conflict reporting is assumed to be negative, and the tone it then reports is negative. One absence worth noting: the script has no leftover prompt text in it. The tilt is a choice somebody coded, not a sign the code was machine-written.

**The saved scores look hand-entered, and they contradict themselves.** The repository's saved score sheet has nine rows. All nine are for a single monument, Avram Iancu, though the paper analyzes four, and the repository contains no scored output at all for the other 52 articles or the other three monuments. That alone means the published results cannot be reproduced from the published data.

The nine rows that do exist look typed in by hand. Within one numeric column, the cells are stored as three different data types: some as text using the Unicode minus sign, some as plain integers, some as decimals with an ordinary hyphen. A Unicode minus is what you get from copy-pasting out of a chat window, a web page, or a word processor; arithmetic does not produce one. The file cannot say which source it was, and it does not have to. Three data types in one column is a signature of manual editing, not of a uniform automated pipeline.

The formatting is circumstantial. The contradictions are not. The same label, "Consider policy option," is attached to two different event codes (140 and 014). Two rows cover the same 19 November 2023 statement by mayor Emil Boc about the Avram Iancu statue, as reported by two outlets. They are coded in opposite directions: one as "praise" with a tone of +0.4, the other as "consider policy option" with a tone of -1. Same event, opposite sentiment, in the file the paper says supports its results.

**The text shows a generation signature, and the AI disclosure does not cover it.** On page 9, one sentence appears three times, twice back to back: "...recent media discourse surrounding certain monuments has shifted towards more emotionally charged narratives, reflecting renewed symbolic negotiations in the city's public space. This reconfirms that recent media discourse surrounding certain monuments has shifted towards more emotionally charged narratives, reflecting renewed symbolic negotiations in the city's public space." A near-identical third version sits a few paragraphs above.

Verbatim self-restatement like this is a common signature of generated text that was not proofread against itself. The paper's "Declaration of AI in Scientific Writing" states only that "the authors used Grammarly in order to proofread the English version of the manuscript." Grammarly does not produce a paragraph that repeats itself word for word. If a generative model drafted text here, the declaration does not disclose it.

None of this requires guessing at intent. It is reproducible from public files in a few minutes: clone the repo, count the dates, read the script's default arguments, open the score sheet, and read page 9 of the open-access PDF.

## Case Two: The Algorithm Was Right, the Write-Up Was Not

The same author, Bogdan Oancea, posted a single-author preprint on arXiv on 4 May 2026: "Unsupervised Machine Learning for Detecting Structural Anomalies in European Regional Statistics" ([arXiv:2605.02884](https://arxiv.org/abs/2605.02884)). I read the full PDF, cloned the code, and reran its data queries, so everything in this section is firsthand.

The method is reasonable. Take 260 European NUTS2 regions, four indicators each (GDP per capita, unemployment, tertiary education, population density), run five standard outlier detectors, and flag any region at least three of them agree is unusual. Eleven regions survive. The detectors did exactly what such detectors do: they flagged capital cities and extreme cases. The failures are in the write-up, and in the data the write-up never checked.

**Four of the eleven regions are mislabeled.** The codes are public, stable, and maintained by Eurostat; the same API the paper pulled its data from returns the official name for every code. I checked the paper's own Table 2 against those labels:

- The paper calls **ES63** "Castilla-La Mancha" and **ES64** "Extremadura," grouped as "Southern Spanish regions" with "demographic pressure." ES63 is Ceuta and ES64 is Melilla, two small Spanish cities on the North African coast. The giveaway is in the author's own table: it lists population densities of 4,152 and 6,086 people per square kilometer for these regions. Those are right for Ceuta and Melilla. Castilla-La Mancha and Extremadura, which are ES42 and ES43, are among the emptiest regions in Europe at roughly 25 to 30 per square kilometer. The right data carries the wrong name.
- The paper calls **HU11** "Northern Hungary" and files it among "structurally weaker" regions with "low tertiary attainment," describing it as "marked by low education." HU11 is Budapest. Its tertiary education figure in the paper's own table is 55.8 percent, the single highest number in the entire table, above Prague, Berlin, and Vienna. The prose calls the most-educated region in the study a low-education region, a few lines from the table that says the opposite. Northern Hungary is HU31. Budapest became HU11 in the NUTS revision that took effect in 2018, the kind of update an out-of-date lookup would miss.
- The paper calls **SK04** "Western Slovakia." SK04 is Eastern Slovakia. Western Slovakia is SK02.

That is a 36 percent error rate on the most basic checkable fact in the paper: which place is this.

**The unemployment column is the wrong Eurostat series, and only the code shows it.** The preprint names its script and links it: `regional_anomaly_detection2.py` at [github.com/bogdanoancea/stat](https://github.com/bogdanoancea/stat). Read the function that downloads unemployment and the bug is plain.

It pulls the Eurostat dataset `lfst_r_lfu3rt`, whose full title is "Unemployment rates by educational attainment level and NUTS 2 region." That is not the headline unemployment series. It has a dimension the script never sets: education level. The code filters sex, age, and unit, but never filters education, so several rows survive per region, one for each education band.

A later step keeps, in the paper's own words, "the first non-missing value for that region." The result: for ten of the eleven flagged regions, the published "unemployment rate" is the rate for the least-educated band, people with at most lower-secondary schooling, not the rate for the whole population. I reran the Eurostat queries; the gap is not subtle:

| Region | Paper's "unemployment" | Actual headline rate |
| --- | --- | --- |
| SK04 (Eastern Slovakia) | 63.2 | 10.2 |
| SK03 (Central Slovakia) | 41.6 | 6.6 |
| DE30 (Berlin) | 15.1 | 4.8 |
| HU11 (Budapest) | 10.3 | 2.3 |
| AT13 (Vienna) | 20.7 | 9.2 |

Eastern Slovakia's headline unemployment in 2022 was about 10 percent. The 63.2 percent the paper prints and feeds to its detectors is the unemployment rate of Eastern Slovakians who never got past lower-secondary school. The eleventh region, Prague, breaks the pattern in a way that proves it: its value is the upper-secondary rate, not the lowest band, because Prague's lowest-band figure was missing and the "first non-missing value" rule fell through to whatever came next. The heuristic the paper presents as a virtue, one that "ensures internal consistency without applying any arbitrary aggregation rule," is the exact mechanism that silently swapped in the wrong subgroup. It did not even pick the same subgroup twice.

This matters because unemployment is one of only four variables the detector sees. The paper groups SK03, SK04, and the Spanish cities among regions that "combine low GDP per capita, low tertiary attainment, and elevated unemployment." Their real headline rates: 6.6 for Central Slovakia, 10.2 for Eastern Slovakia, and 30.0 and 25.9 for the two Spanish cities; Budapest, also flagged, sits at 2.3. The elevated unemployment that defines the paper's structurally-weak group is, in several cases, an artifact of reading the wrong row. The other three indicators are fine: I checked all eleven regions, and tertiary education and population density match the Eurostat headline values to the decimal, while GDP matches within ordinary revision. The error is isolated to the one column, and it is the column the story leans on.

**Two clean technical errors.** The paper claims all five methods, "including Mahalanobis distance," are "robust to monotonic transformations." That is false. Mahalanobis distance is defined through the covariance matrix, and a nonlinear transform such as a log changes the covariance and therefore every distance. Separately, the paper sets the machine-learning detectors to flag about 5 percent of regions while holding the classical methods near 1 percent, then reports that the classical methods flagged fewer. The threshold settings create that gap.

**Then there are the citations.** I ran the preprint's fourteen references through Crossref, OpenAlex, and the cited journals' own tables of contents. Six do not exist. They carry the standard hallucination signature: real journals, plausible volumes and years, page ranges that collide with two real articles each, and a real senior academic's name paired with an untraceable co-author. A seventh looked suspect too, an institutional report from UNECE, but that one turned out to be real; the six fakes, as printed in the preprint, are in the sources at the end. These are exactly the hallucinated references arXiv's new policy is meant to catch.

Here is the twist. This preprint is also a published article: its arXiv page carries a reference to Romanian Statistical Review, no. 1 (2026), pages 3 to 22, posted online on 30 March 2026, more than a month before the arXiv version. I solved the publisher site's bot challenge, downloaded the published PDF, and diffed it against the preprint.

The published version removed all six fabricated references and replaced them with real ones. Whatever review the venue applied caught the hallucinated citations, and caught nothing else. The mislabeled regions are unchanged: ES63 is still "Castilla-La Mancha," and HU11 is still "Northern Hungary," narrated as low-education at its table-topping 55.8 percent. The false Mahalanobis claim survives word for word. The first-non-missing rule that corrupted the unemployment column survives word for word, twice. Eastern Slovakia's 63.2 percent is printed in a peer-reviewed journal as its unemployment rate, and the published version carries no AI-use disclosure of any kind.

This is the more instructive of the two cases, because it points to carelessness rather than manipulation. Someone massaging results makes the numbers and the story agree. This paper does the opposite: it prints a table saying Budapest has the highest education in the set, then narrates Budapest as having the lowest. The contradiction sits in plain view, in a published journal, because at no point did anyone, author or reviewer, check the prose against the table in the same section. The prose reads like regional-economics expertise wrapped around a lookup error.

## The Common Thread: One Author, a Citation Mountain, and a Network

Three real papers run through one author. The scorecard so far:

| Paper | Authors | What I found |
| --- | --- | --- |
| Monument analysis (Geographical Journal, Feb 2026) | Nicula, Cretan, Dragan, Oancea | Corpus does not match its description; biased scoring; self-contradicting data; AI text signature |
| NUTS2 anomalies (Romanian Statistical Review / arXiv, 2026) | Oancea, solo | Mislabeled regions; wrong-subgroup unemployment; fabricated citations fixed only in the journal version |
| Inflation forecasting (Prague Economic Papers, Dec 2025) | Oancea, Simionescu, Pospisil | Two items worth stating, below |

The third paper is "Do Machine Learning Techniques Outperform Autoregressive Distributed Lag Models in Inflation Forecasting?" (DOI [10.18267/j.pep.898](https://doi.org/10.18267/j.pep.898)). This one I could only read; there was no repo to clone.

**The hardware is far out of proportion to the data.** The limitations section states, verbatim, that "optimizing the LSTM took more than a week of computing time on a computer with an Intel(R) Xeon(R) Gold 6226R CPU @ 2.90GHz processor, 16 cores, and 192 GB of RAM." The Romania dataset is quarterly, 2006 through 2023: 72 quarters, of which 66 are used. That is a few kilobytes. An LSTM on a series this short trains in seconds on a laptop. A 192 GB server and a week of compute are not impossible, and may reflect a large unreported hyperparameter search, but nothing in the problem justifies the spec.

**The model reports test error far below training error, and the paper says so openly.** In the monthly results table, for Romania, the training MAPE is 6.98 percent and the test MAPE is 0.58 percent, more than ten times lower on data the model did not train on. Whether that inversion means a leak between training and test is a separate question, and it stays in the unverified bucket; the next section says why.

Then there is the citation count. Oancea's Google Scholar profile shows roughly 86,000 citations and an h-index of 73, and his University of Bucharest faculty page advertises "over 80,000 citations." His five most-cited papers are all Global Burden of Disease consortium articles in The Lancet and its sister journals; the largest, a single 2020 Lancet paper with more than a thousand named authors, has been cited over 15,000 times on its own. This is hyper-authorship: being one of hundreds or thousands of named contributors on enormous consortium papers, a known and entirely legal mechanism that inflates an individual's citation metrics far beyond anything their own field could generate. The numbers are real. The point is only that they are not evidence of anything about the work above; a citation mountain built from medical consortia tells you nothing about whether a regional-statistics script filtered its data correctly.

His co-author Mihaela Simionescu sits in a more concrete situation. On 7 August 2025, Frontiers retracted a 2021 paper of hers in Frontiers in Environmental Science, one of [122 articles pulled in a single action](https://retractionwatch.com/2025/07/29/frontiers-retract-122-articles-links-thousands-other-publishers-journals-to-unethical-network/). The publisher's research-integrity team described "a network of authors and editors who conducted peer review with undisclosed conflicts of interest and ... citation manipulation." The [retraction notice](https://www.frontiersin.org/journals/environmental-science/articles/10.3389/fenvs.2025.1675246/full) is careful on two points that matter for fairness: the investigation "was not able to determine whether all authors, editors, or reviewers were aware of or involved in the misconduct," and "the authors do not agree to this retraction." Two further Simionescu papers carry PubPeer comments flagging author-reviewer conflicts of interest, and one carries an automated screener's flag on PubPeer for questioned references. Oancea and Pospisil are not authors on the retracted paper, and neither has any PubPeer thread or retraction I could find.

So the verified skeleton is this: three real papers by Oancea, two of them with reproducible problems I checked myself, a citation total inflated by medical hyper-authorship, and one co-author with a disputed retraction inside a publisher-identified network. Worth taking seriously as a pattern in the published record. Not, on this evidence alone, a claim about anyone's intent.

## What I Could Not Pin Down

Two things I am keeping at their true confidence level rather than overstating:

- **I verified the test-below-training MAPE inversion as a number; I could not verify data leakage as its cause.** Test error below training error is unusual and worth scrutiny, but it is not impossible. With a percentage-error metric like MAPE, a test window whose values are larger can show a smaller percentage error for ordinary reasons. The paper discloses the pattern rather than hiding it, and reports it across many countries, with the direction mixed: Bulgaria, in the same table, is the reverse, with training error far below test. Suggestive, not dispositive.
- **The integrity-database trail runs through one co-author, not the group.** The [For Better Science](https://forbetterscience.com/2025/08/08/schneider-shorts-8-08-2025-increasingly-hostile-and-now-defamatory-attacks/) blog covered the Frontiers mass retraction and listed Simionescu's paper, but it is not a dedicated investigation of this group, its headline refers to an unrelated matter, and it does not mention Oancea or Pospisil. Anyone tempted to read a wider network into it should stop at what the record supports.

## A Short Tour of the Neighborhood

None of this is unusual in kind, only in how cleanly it shows the seam. I read the three preprints below, a lighter pass than the two main cases; I did not rerun their code or their numbers.

- **[AutoMat](https://arxiv.org/abs/2605.00803)**, a Johns Hopkins benchmark, tests whether coding agents can reproduce findings from real computational materials science papers. The best setup succeeds about half the time, and almost never when it has to reconstruct a method from prose alone. Its sharpest case study: an agent matched a reported accuracy of 0.89 to within 0.0006, by quietly evaluating on a different subset than the claim specified. Right number, wrong science.
- **[SpecKV](https://arxiv.org/abs/2605.02888)**, a single-author preprint on speculative decoding, reports a "56 percent improvement" measured in a simulation that uses the same predictor the method is being graded on, with no end-to-end benchmark, against a trivial fixed baseline that matches it to a rounding error.
- **[RecursiveMAS](https://arxiv.org/abs/2604.25917)** makes a clean architectural argument for agents that pass hidden states instead of text, then benchmarks it against a baseline whose accuracy drops as you give it more compute, a strong hint, though only a hint, that the baseline was never tuned.

None of these is fraud. In every one, the machinery ran and produced a confident artifact. The skipped step was the one that would have caught the problem: checking that the number still meant what it was supposed to mean. It is now cheap to generate something that looks checked.

## The Rules That Fall Out

1. **Demand the artifact, not the description of it.** Every paper claim I could trace to an actual file either held up or failed cleanly. The ones that lived only in prose stayed open until I reached the file behind them.
2. **Raise the bar for fluent documents, do not lower it.** Polish is free now. A paper can read like expertise all the way down and still be narrating the wrong row, and arXiv's new ban treats unchecked AI output as a submission-level offense for exactly this reason.
3. **Keep confidence tiers explicit, never merge them, and hold claims about named people to the highest bar.** A document that blends reproducible facts, unchecked claims, and fabrications into a single confident voice is more dangerous than one that is wrong throughout, because the solid parts launder the rest. The cost of getting this wrong about a real person is not symmetric with the cost of getting it right.

## The Honest Summary

Two published papers by one author have serious problems I verified myself from primary sources: a stated corpus window that the data does not support, a scoring instrument tuned toward its own conclusion, four mislabeled regions, and a headline unemployment column that is silently the wrong demographic. A third paper carries a hardware spec far beyond its data and a real metric inversion, and around it sit a citation mountain built from medical consortia and a co-author's disputed retraction.

The clearest signal of all is the journal version of the regional paper, where review stripped the hallucinated citations and left every substantive error in place. None of it required trusting anyone's description of anything; all of it came from files I could open. Trust, but clone the repo.

## Disclaimer

This post discusses real, named researchers and their publicly available work. The problems in the two main cases are reproducible from primary sources, two public code repositories, the papers' own PDFs, and Eurostat's public API, and I present them as documented errors and undisclosed-AI signatures, not as a legal finding of misconduct.

"Fraud" in the strict sense requires intent to deceive. The reproducible facts here do not by themselves establish intent, and in the regional-statistics case the internal contradictions argue against it. Where a third party has taken formal action, such as the Frontiers retraction, I have quoted that action's own language, including that the investigation did not determine individual involvement and that the authors dispute it.

I have not sought comment from the authors or journals before publishing; the artifacts speak for themselves, and any formal correction belongs to the venues' own processes. Anything the evidence does not settle I have labeled unverified, and it stays unattached to any individual as fact. If any author can show that an item I marked verified is wrong, I will correct it.

## Verification Status of Every Claim

The table covers the two case studies. I flagged the neighborhood tour in place as a lighter pass.

| Claim | Status | How I checked it |
| --- | --- | --- |
| Monument paper exists, DOI 10.1111/geoj.70073, authors Nicula/Cretan/Dragan/Oancea, Geographical Journal, Feb 2026 | Verified firsthand | Crossref API |
| Repo github.com/bogdanoancea/sentometric holds the code and data, single commit dated 21 Feb 2026 | Verified firsthand | Cloned the repo, read git log |
| Corpus is 61 articles; data runs July 2011 to January 2025; none before 2011; full gap 2017-2020; 21 of 61 from 2023 | Verified firsthand | Parsed the dataset |
| Per-monument counts 20 / 15 / 14 / 9; three rows lack date and monument; no scored output for the other 52 articles | Verified firsthand | Parsed the dataset and repo |
| Paper's claim: corpus covers "January 1990 to November 2025" | Falsified | Data section of the PDF vs the dataset (July 2011 to January 2025) |
| Script defaults bias sentiment negative via five mechanisms; paper discloses the asymmetry | Verified firsthand | Read the script defaults and the paper methods |
| Score sheet: 9 rows, one monument, mixed data types, same event coded oppositely | Verified firsthand | Inspected the score sheet |
| Page-9 sentence repeated three times (twice back to back); AI declaration names only Grammarly | Verified firsthand | Read pages 9 and 11 of the paper PDF |
| NUTS2 paper exists, arXiv:2605.02884, published as Romanian Statistical Review no. 1 (2026): 3-22 | Verified firsthand | arXiv journal-ref field and both PDFs |
| 4 of 11 flagged regions mislabeled (ES63, ES64, HU11, SK04) | Verified firsthand | Paper's Table 2 vs the Eurostat API's own region labels |
| HU11 is Budapest, narrated as "low education" while its own table shows 55.8 percent, the highest | Verified firsthand | The PDF, Table 2 and discussion |
| Unemployment column is the ED0-2 (least-educated) subgroup rate for 10 of 11 regions, not the headline rate | Verified firsthand | Cloned github.com/bogdanoancea/stat, reran Eurostat lfst_r_lfu3rt queries |
| GDP, tertiary education, and density columns match the Eurostat headline values | Verified firsthand | Reran the same Eurostat queries |
| Paper's claim: all methods including Mahalanobis are "robust to monotonic transformations" | Falsified | The PDF text plus the definition of the metric |
| 5 percent vs 1 percent threshold asymmetry drives the "less conservative" finding | Verified firsthand | The PDF, methods and Table 1 |
| Dataset is 301 rows including EA20/EFTA aggregates, not the stated 260 regions | Verified firsthand | The repo's output CSV and the paper's Table 1 arithmetic |
| 6 of 14 preprint references are fabricated; the journal version replaced them with real ones | Verified firsthand | Crossref, OpenAlex, journal TOCs; diffed preprint vs journal PDF |
| Journal version keeps every data error and carries no AI disclosure | Verified firsthand | Diffed the two PDFs |
| Prague Economics paper exists, DOI 10.18267/j.pep.898, Oancea/Simionescu/Pospisil, Dec 2025 | Verified firsthand | Crossref API and the open-access PDF |
| Hardware: Xeon Gold 6226R, 16 cores, 192 GB RAM, "more than a week" of compute, on a kilobyte-scale dataset | Verified firsthand | Quoted from the PDF limitations section |
| Test MAPE 0.58 percent below training MAPE 6.98 percent (Romania, monthly table); disclosed, not hidden | Verified firsthand | Read the PDF's monthly results table |
| That inversion is proof of data leakage | Unverified | Verified the numbers; leakage is one possible cause among others, not established |
| Oancea has about 86,000 citations, bulk from GBD / Lancet consortium papers | Verified | Google Scholar profile, corroborated by University of Bucharest page |
| Simionescu 2021 Frontiers paper retracted 7 Aug 2025 in a network action; authors dispute it; no individual named culpable | Verified | Frontiers notice, Crossref, Retraction Watch |
| Oancea and Pospisil: no PubPeer thread or retraction found | Verified | PubPeer, Crossref/Retraction Watch metadata |
| arXiv one-year ban for unchecked AI content, May 2026 | Verified | TechCrunch, Nature reporting |

*"Unverified" means the evidence I have does not settle the item either way. It is neither a confirmation nor a denial; confirm it yourself before relying on or repeating it, especially when it names a person. Rows marked plain "Verified" rest on the public record named in the row, a profile, a notice, or news reporting, rather than on an artifact you can rerun.*

## Sources

I resolved every link below while writing this post, checked the DOIs against Crossref and the arXiv identifiers against the arXiv API, and read the PDFs in full.

### The Papers and Their Data (Primary Sources)

- Alexandru-Sabin Nicula, Remus Crețan, Alexandru Dragan, and Bogdan Oancea, ["Computational Analysis of Contested Monuments and Collective Memory in a Multiethnic City"](https://doi.org/10.1111/geoj.70073), *The Geographical Journal* 192 (2026): e70073. DOI: [10.1111/geoj.70073](https://doi.org/10.1111/geoj.70073). Open access.
- Bogdan Oancea, the analysis code and dataset for the paper above: [github.com/bogdanoancea/sentometric](https://github.com/bogdanoancea/sentometric) (`monument-analysis.py`, `monument-data.xlsx`).
- Bogdan Oancea, ["Unsupervised Machine Learning for Detecting Structural Anomalies in European Regional Statistics"](https://arxiv.org/abs/2605.02884), arXiv:2605.02884, 4 May 2026; published as *Romanian Statistical Review* no. 1 (2026): 3-22.
- Bogdan Oancea, the analysis code for the paper above: [github.com/bogdanoancea/stat](https://github.com/bogdanoancea/stat) (`regional_anomaly_detection2.py`), and the Eurostat unemployment dataset it pulls, [`lfst_r_lfu3rt`](https://ec.europa.eu/eurostat/databrowser/view/lfst_r_lfu3rt/default/table) ("Unemployment rates by educational attainment level and NUTS 2 region").
- The six fabricated references, as printed in the arXiv version of the paper above (none exists; the journal version replaced all six with real citations): Balducci and Gervasoni, "Detecting structural breaks and spatial anomalies in regional development: A clustering perspective," *Regional Science Policy and Practice* 10(4), 2018; European Statistical System, "ESS handbook on statistical data editing," 2022; Giannotti, Barlacchi, and Pappalardo, "Uncovering hidden patterns in regional development using unsupervised learning," *Environment and Planning B* 49(7), 2022; James, McLaren, and Yigitcanlar, "Spatial anomaly detection for labour market dynamics: A machine learning approach," *Regional Studies* 55(9), 2021; Kemper, Brakman, and Garretsen, "Identifying resilient and vulnerable regions through unsupervised anomaly detection," *Papers in Regional Science* 102(1), 2023; Reiss and Haase, "Regional differentiation in Europe: A multivariate analysis of socio-economic structures," *European Planning Studies* 28(10), 2020.
- Bogdan Oancea, Mihaela Simionescu, and Richard Pospisil, ["Do Machine Learning Techniques Outperform Autoregressive Distributed Lag Models in Inflation Forecasting?"](https://doi.org/10.18267/j.pep.898), *Prague Economic Papers* 34, no. 4 (2025): 495-558. DOI: [10.18267/j.pep.898](https://doi.org/10.18267/j.pep.898). Open-access [PDF](https://pep.vse.cz/pdfs/pep/2025/04/03.pdf).

### Citation Metrics and the Integrity Record

- Bogdan Oancea, [Google Scholar profile](https://scholar.google.com/citations?user=MHVWyc8AAAAJ) (about 86,000 citations as accessed), corroborated by his [University of Bucharest faculty page](https://unibuc.ro/user/bogdan.oancea/?lang=en) ("over 80,000 citations").
- GBD 2019 Diseases and Injuries Collaborators, ["Global burden of 369 diseases and injuries in 204 countries and territories, 1990-2019"](https://doi.org/10.1016/S0140-6736(20)30925-9), *The Lancet* 396 (2020): 1204-1222. The most-cited item on Oancea's profile, with more than a thousand listed authors; an illustration of hyper-authorship.
- Mihaela Simionescu et al., ["The Impact of Quality of Governance, Renewable Energy and Foreign Direct Investment on Sustainable Development in Cee Countries"](https://www.frontiersin.org/journals/environmental-science/articles/10.3389/fenvs.2021.765927/full), *Frontiers in Environmental Science* (2021), and its [retraction notice](https://www.frontiersin.org/journals/environmental-science/articles/10.3389/fenvs.2025.1675246/full) (7 August 2025).
- Retraction Watch, ["Frontiers to retract 122 articles, links thousands in other publishers' journals to 'unethical' network"](https://retractionwatch.com/2025/07/29/frontiers-retract-122-articles-links-thousands-other-publishers-journals-to-unethical-network/), 29 July 2025.
- Leonid Schneider, ["Schneider Shorts 8.08.2025"](https://forbetterscience.com/2025/08/08/schneider-shorts-8-08-2025-increasingly-hostile-and-now-defamatory-attacks/), For Better Science (lists the retracted Simionescu paper; does not name Oancea or Pospisil).
- PubPeer, [comment threads for "Mihaela Simionescu"](https://pubpeer.com/search?q=%22Mihaela+Simionescu%22) (the only one of the three authors with entries).

### Geographic Codes and Methods

- Eurostat, [NUTS classification (NUTS 2021)](https://ec.europa.eu/eurostat/web/nuts/); the disputed codes confirmed from the Eurostat API's own region labels and corroborated via Wikipedia ([Spain](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Spain), [Hungary](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Hungary), [Slovakia](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Slovakia)). ES63 = Ciudad de Ceuta, ES64 = Ciudad de Melilla, HU11 = Budapest, SK04 = Vychodne Slovensko (Eastern Slovakia).
- Philip A. Schrodt, [CAMEO: Conflict and Mediation Event Observations, Codebook v1.1b3](https://data.gdeltproject.org/documentation/CAMEO.Manual.1.1b3.pdf) (the event ontology the monument paper uses).
- C. J. Hutto and Eric Gilbert, ["VADER: A Parsimonious Rule-Based Model for Sentiment Analysis of Social Media Text"](https://ojs.aaai.org/index.php/ICWSM/article/view/14550), ICWSM 2014 (the lexicon the pipeline extends).

### The AI-Content Crackdown and the Reproducibility Backdrop

- Dalmeet Singh Chawla, ["Researchers who use hallucinated references to face arXiv ban"](https://www.nature.com/articles/d41586-026-01595-5), *Nature* news, 19 May 2026.
- Devin Coldewey, ["Research repository arXiv will ban authors for a year if they let AI do all the work"](https://techcrunch.com/2026/05/16/research-repository-arxiv-will-ban-authors-for-a-year-if-they-let-ai-do-all-the-work/), TechCrunch, 16 May 2026.
- ["AutoMat": Can Coding Agents Reproduce Findings in Computational Materials Science?](https://arxiv.org/abs/2605.00803), arXiv:2605.00803, 2026.
- SpecKV: ["Adaptive Speculative Decoding with Compression-Aware Gamma Selection"](https://arxiv.org/abs/2605.02888), arXiv:2605.02888, 2026.
- ["Recursive Multi-Agent Systems"](https://arxiv.org/abs/2604.25917), arXiv:2604.25917, 2026.
