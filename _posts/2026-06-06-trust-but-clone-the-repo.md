---
title: "Trust, but Clone the Repo: Fact-Checking AI-Assisted Academic Papers"
date: 2026-06-06 10:00:00 -0400
categories: [AI, Research]
tags: [ai, research-integrity, reproducibility, arxiv, machine-learning, academic-publishing]
description: "A reproducible audit of AI-assisted academic papers shows why checking code, data, citations, and regional labels matters more than polished prose."
media_subpath: /assets/img/posts/2026-06-06-trust-but-clone-the-repo/
image:
  path: cover.png
  alt: "Repository, paper, and verification checks connected in an audit workflow"
math: false
mermaid: false
---

In May 2026, [arXiv announced](https://www.nature.com/articles/d41586-026-01595-5) that authors who submit papers with clear signs of unchecked AI output, such as hallucinated references or chatbot instructions left in the text, would be banned from submitting for a year, after which they would have to clear peer review before posting again. The arXiv computer science chair, Thomas Dietterich, framed it as a response to volume: submissions are up by half since ChatGPT, desk rejections are up several times over, and a Lancet study found fabricated citations in biomedical papers climbing fast. The machine that writes plausible academic prose has met the machine meant to check it, and the checking is losing.

This post is about that first failure: papers that look finished but were never checked by a human against their own data. I worked through a single connected case, centered on one prolific author, and verified every claim against the primary source. The post asks a narrow question, whether the published work holds up when you check it, and shows a method you can run yourself.

A note on confidence, because it is the whole point. Every claim below sits in one of three buckets. **Verified firsthand** means I examined the primary source, usually in code, and you can reproduce it. **Unverified** means the claim came from a source I could not confirm, and it stays a lead, not a fact. **Falsified** means I checked and it was wrong.

I never let a verified fact lend its credibility to an unverified one sitting next to it. When real, named people are involved, that discipline is not optional. There is a full status table at the end, and a disclaimer about what these findings do and do not establish.

![Repository, paper, and verification checks connected in an audit workflow](preview.svg){: w="700" h="394" .shadow }
_Polished prose is cheap. Reproducible verification still has to touch the files._

## Case One: A Paper Whose Data Does Not Match Its Own Description

Start with what anyone can pull, because it is the part I can fully stand behind.

In February 2026 The Geographical Journal published "Computational Analysis of Contested Monuments and Collective Memory in a Multiethnic City" (Nicula, Cretan, Dragan, and Oancea; DOI [10.1111/geoj.70073](https://doi.org/10.1111/geoj.70073)). It runs a computational sentiment pipeline over Romanian news coverage of four monuments in Cluj-Napoca and concludes that public discourse around them grew more negative and intense after 2022. The paper's data availability statement points to a public repository, [github.com/bogdanoancea/sentometric](https://github.com/bogdanoancea/sentometric). I cloned it. It holds the analysis script and the dataset, in a single commit dated 21 February 2026, the day after publication. Here is what they say.

**The corpus is 61 articles, and its shape, not the world, produces the trend.** According to the paper's methods section, the media corpus covers "the period January 1990 to November 2025," a span of nearly 36 years. In the repository dataset, coverage starts in July 2011. There is nothing before 2011, nothing at all in 2017, 2018, 2019, or 2020, and the latest article is from January 2025.

So both ends of the stated window are overstated: the corpus begins about 21 years after the claimed start and ends about 10 months before the claimed finish. Twenty-one of the 61 articles, a third of the entire corpus, come from 2023 alone. When a third of your evidence is from one recent year and most of the stated window is simply empty, a finding that discourse "intensified after 2022" is hard to separate from the obvious fact that recent online articles are easier to find. A trend built into how the corpus was assembled is not a discovery about the world. For the record, the per-monument counts are Scoala Ardeleana 20, Matthias Corvinus 15, Baba Novac 14, Avram Iancu 9, and three of the 61 rows carry a headline with no date or monument filled in at all.

**The scoring instrument is tuned, by default, toward the result the paper reports.** The published script does not weigh positive and negative sentiment evenly. Reading the default parameters in the code:

- Positive sentiment scores are multiplied by 0.60 and negative scores by 1.15 before anything else. Positives are shrunk to 60 percent of their value, negatives are amplified. The code comment calls this "shrink positives, slightly amplify negatives."
- The neutral band is asymmetric. A score has to clear +0.10 to count as positive but only fall to -0.03 to count as negative, so a faintly negative article is labeled negative while an equally faint positive one is called neutral.
- Two separate "vandalism" rules subtract a fixed amount from the tone of any article containing trigger words.
- A "domain prior," switched on by default, takes any article whose event code falls in a hostile or destructive family and, if it is not already labeled negative, forces the label to negative and subtracts a further penalty from its tone.

Five mechanisms point the same direction before a single result is read. To its credit, the paper discloses the asymmetry rather than hiding it: the methods section says it "applied an asymmetric scaling transformation that attenuates positive scores and amplifies negative ones," that "negative classification required weaker evidence than positive classification," and that destructive event codes "could not be assigned a strongly positive" sentiment.

The stated justification is also the conclusion. The instrument is tuned to read negative because conflict reporting is assumed to be negative, and the paper then reports that the tone is negative. The loop is close to circular. To be precise about scope, the script contains no leftover AI prompt text, so this is a design choice in the code, not evidence that the code itself was machine-written.

**The saved scores look hand-entered, and they contradict themselves.** The repository's saved score sheet has nine rows. All nine are for a single monument, Avram Iancu, though the paper analyzes four. Within one numeric column the cells are stored as three different data types: some as text using the Unicode minus sign (the character you get from copy-pasting, not from arithmetic), some as plain integers, some as decimals with an ordinary hyphen. That mixture is a signature of manual editing, not of a uniform automated pipeline.

The same label, "Consider policy option," is attached to two different event codes (140 and 014). And two rows, both dated 19 November 2023, both covering the same statement by mayor Emil Boc about the Avram Iancu statue, taken from two outlets, are coded in opposite directions: one as "praise," with a positive tone of +0.4, the other as "consider policy option," with a tone of -1. Same event, opposite sentiment, in the file the paper says supports its results.

**The text shows a generation signature, and the AI disclosure does not cover it.** On page 9, one sentence appears three times, twice back to back. The published wording: "...which reconfirms that recent media discourse surrounding certain monuments has shifted towards more emotionally charged narratives, reflecting renewed symbolic negotiations in the city's public space. This reconfirms that recent media discourse surrounding certain monuments has shifted towards more emotionally charged narratives, reflecting renewed symbolic negotiations in the city's public space." A near-identical third version ("This suggests that...") sits a few paragraphs above.

Verbatim self-restatement like this is a common signature of generated text that was not proofread against itself. The paper's "Declaration of AI in Scientific Writing" states only that "the authors used Grammarly in order to proofread the English version of the manuscript." Grammarly proofreads; it does not produce a paragraph that repeats itself word for word. If a generative model drafted text here, the declaration does not disclose it.

None of this requires guessing at intent. It is reproducible from public files in a few minutes: clone the repo, count the dates, read the script's default arguments, open the score sheet, and read page 9 of the open-access PDF. Reproducible findings have this shape: hand someone the steps and they get the same result.

## Case Two: The Algorithm Was Right, the Author Mislabeled the Map

The same author, Bogdan Oancea, posted a single-author preprint on arXiv on 4 May 2026: "Unsupervised Machine Learning for Detecting Structural Anomalies in European Regional Statistics" ([arXiv:2605.02884](https://arxiv.org/abs/2605.02884)). I have the full PDF, so this one is verified firsthand end to end.

The method is reasonable. Take 260 European NUTS2 regions, four indicators each (GDP per capita, unemployment, tertiary education, population density), run five standard outlier detectors, and flag any region that at least three of them agree is unusual. Eleven regions survive. The algorithms did exactly what such detectors do: they flagged capital cities and extreme cases. The failure is entirely in the write-up, where region codes get turned into place names and then into stories.

The codes are public and stable. Eurostat maintains them. Checking the paper's own Table 2 against the classification (corroborated through Wikipedia and Google Data Commons, since Eurostat's own server blocks automated requests):

- The paper calls **ES63** "Castilla-La Mancha" and **ES64** "Extremadura," and groups them as "Southern Spanish regions" with "demographic pressure." ES63 is Ceuta and ES64 is Melilla, two small Spanish cities on the North African coast. The giveaway is in the author's own table: he lists population densities of 4,152 and 6,086 people per square kilometer for these regions. Those are right for Ceuta and Melilla. Castilla-La Mancha and Extremadura, which are ES42 and ES43, are among the emptiest regions in Europe at roughly 25 to 30 per square kilometer. He had the right data and put the wrong names on it.
- The paper calls **HU11** "Northern Hungary" and files it among "structurally weaker" regions "marked by low tertiary attainment." HU11 is Budapest. Its tertiary education figure in the paper's own table is 55.8 percent, the single highest number in the entire table, above Prague, Berlin, and Vienna. The prose calls the most-educated region in the study "low education," a few lines from the table that says the opposite. Northern Hungary is HU31. Budapest became HU11 in the NUTS revision that took effect in 2018, when the old central region was split, the kind of update an out-of-date lookup would miss.
- The paper calls **SK04** "Western Slovakia." SK04 is Eastern Slovakia. Western Slovakia is SK02.

That is four mislabeled regions out of eleven, a 36 percent error rate on the most basic checkable fact in the paper: which place is this. There are also two clean technical errors. The paper claims all five methods, "including Mahalanobis distance," are "robust to monotonic transformations," which is false. Mahalanobis distance is defined through the covariance matrix, and a nonlinear transform such as a log changes the covariance and therefore every distance. And it sets the machine-learning detectors to flag about 5 percent of regions while holding the classical methods near 1 percent, then reports the unsurprising "finding" that the classical methods flagged fewer. The threshold settings create that gap.

This case is the more instructive of the two, because it points to carelessness rather than manipulation. Massaging results means making the story match the numbers. This paper does the opposite: it prints a table saying Budapest has the highest education in the set, then narrates Budapest as having low education. That contradiction sits in plain view.

It is the fingerprint of fluent text produced or assembled without anyone checking it against the table in the same section. The prose reads like regional-economics expertise. It is expertise-shaped text wrapped around a lookup error.

The preprint also documents, in its own methods, a data-handling rule worth naming. When several values exist for the same region and indicator, "the script automatically retains the first non-missing value." That is a silent tie-breaker that can change which underlying figure represents a region, with no flag to the reader. I am quoting the paper directly; this is verified.

## The Common Thread: One Author, a Citation Mountain, and a Network

Oancea is a co-author on the monument paper and the sole author of the regional-statistics preprint. He also co-authored, with Mihaela Simionescu and Richard Pospisil, a December 2025 article in Prague Economic Papers, "Do Machine Learning Techniques Outperform Autoregressive Distributed Lag Models in Inflation Forecasting?" (DOI [10.18267/j.pep.898](https://doi.org/10.18267/j.pep.898)). I confirmed all three papers exist and are correctly attributed, through Crossref and arXiv, and I read the Prague paper in full from its open-access PDF.

Two things in that inflation paper are worth stating, both verified from the primary source by reading the open-access PDF in full.

**The hardware is wildly out of proportion to the data.** In its limitations section the paper states, verbatim: "optimizing the LSTM took more than a week of computing time on a computer with an Intel(R) Xeon(R) Gold 6226R CPU @ 2.90GHz processor, 16 cores, and 192 GB of RAM." The Romania dataset is quarterly data for 2006 through 2023, that is 72 quarters, of which 66 are used (62 training, 4 test). That is a few kilobytes. An LSTM on a series this short trains in seconds on a laptop. A 192 GB server and a week of compute are not impossible, and may reflect a large unreported hyperparameter search, but the spec is not justified by the problem and reads as theatre.

**The model reports test error far below training error, and the paper says so openly.** In the monthly results table, for Romania, the LSTM's training MAPE is 6.98 percent and its test MAPE is 0.58 percent, more than ten times lower on data it did not train on. That pattern is unusual and is the kind of thing that can indicate a leak between training and test. It is worth scrutiny.

But this matters: test-below-training error is possible. With a percentage-error metric like MAPE, a test window whose values are larger can show a smaller percentage error for ordinary reasons. The paper does not hide the inversion; it reports it across many countries, and the pattern is mixed (Bulgaria, in the same table, is the reverse, with training error far below test). Suggestive, not dispositive.

Then there is the citation count. Oancea's Google Scholar profile shows roughly 86,000 citations, an h-index of 73, and his own University of Bucharest faculty page advertises "over 80,000 citations." His listed interests include "Public Health" and "Health Metrics," which for a professor in a business and administration faculty is the tell. His five most-cited papers are all Global Burden of Disease consortium articles in The Lancet and its sister journals. The largest, a single 2020 Lancet paper with more than a thousand named authors, has been cited over 15,000 times on its own.

This is hyper-authorship: being one of hundreds or thousands of named contributors on enormous consortium papers, which then inflates an individual's citation metrics far beyond the reach of the individual's own field. The mechanism is well documented in bibliometrics. The numbers here are verified, and the bulk of the mountain plainly comes from medical consortia, not from economics or statistics.

His co-author Mihaela Simionescu sits in a different and more concrete situation. A 2021 paper of hers in Frontiers in Environmental Science was [retracted on 7 August 2025](https://www.frontiersin.org/journals/environmental-science/articles/10.3389/fenvs.2025.1675246/full), as part of a [mass retraction of 122 articles](https://retractionwatch.com/2025/07/29/frontiers-retract-122-articles-links-thousands-other-publishers-journals-to-unethical-network/) in which the publisher's research-integrity team described "a network of authors and editors who conducted peer review with undisclosed conflicts of interest and who have engaged in citation manipulation."

The retraction notice is explicit on two points that matter for fairness: the investigation "was not able to determine whether all authors, editors, or reviewers were aware of or involved in the misconduct," and "the authors do not agree to this retraction." Two further Simionescu papers carry PubPeer comments flagging author-reviewer conflicts of interest and, in one case, seven questioned references caught by an automated screener. Oancea and Pospisil are not authors on the retracted paper, and neither has any PubPeer thread or retraction that I could find. So the integrity-database trail, such as it is, runs through Simionescu, and even there the publisher stops short of naming an individual.

That is the verified skeleton: three real papers, two of them with reproducible problems I checked myself, a citation total inflated by medical hyper-authorship, and one co-author with a disputed retraction inside a publisher-identified network. Worth taking seriously as a pattern in the published record. Not, on this evidence alone, a claim about anyone's intent.

## What Could Not Be Pinned Down, and What Stays Unproven

Two things I am keeping at their true confidence level rather than overstating:

- The test-below-training error inversion is real and verified, but reading it as proof of data leakage is not. It is unusual and worth scrutiny, the authors disclose it rather than hiding it, and with a percentage-error metric it can arise for benign reasons. Suggestive, not dispositive.
- The [For Better Science](https://forbetterscience.com/2025/08/08/schneider-shorts-8-08-2025-increasingly-hostile-and-now-defamatory-attacks/) blog did cover the Frontiers mass retraction and listed Simionescu's paper, but it is not a dedicated investigation of this group, its headline refers to an unrelated matter, and it does not mention Oancea or Pospisil.

Neither gap is hidden. Both are in the table at the end, at their true confidence level.

## A Short Tour of the Neighborhood

This case is not unusual in kind, only in how cleanly it shows the seam. Spend time reading recent AI and machine-learning papers the way a reviewer would, and the same gap, between what a system produces and what anyone verified, turns up everywhere.

- **[AutoMat](https://arxiv.org/abs/2605.00803)**, a Johns Hopkins benchmark, tests whether coding agents can reproduce findings from real computational materials science papers. The best setup succeeds about half the time, and almost never when it has to reconstruct a method from prose alone. Its sharpest case study: an agent matched a reported accuracy of 0.89 to within 0.0006, by quietly evaluating on a different subset than the claim specified. Right number, wrong science.
- **[SpecKV](https://arxiv.org/abs/2605.02888)**, a single-author preprint on speculative decoding, reports a "56 percent improvement" that turns out to be measured in a simulation using the same predictor the method is being graded on, with no end-to-end benchmark, and a trivial fixed baseline that matches it to a rounding error.
- **[RecursiveMAS](https://arxiv.org/abs/2604.25917)** makes a clean architectural argument for agents that pass hidden states instead of text, then benchmarks it against a baseline whose accuracy drops as you give it more compute, a strong hint the baseline was never tuned.

None of these is fraud. In every one, the machinery ran and produced a confident artifact, and the step that would have caught the problem, checking that the number still meant what it was supposed to mean, was the step that got skipped. It is now cheap to generate something that looks checked.

## The Rules That Fall Out

1. Demand the artifact, not the description of the artifact. Every claim in this case that ran through a file I could open held up or was cleanly false. Every claim that ran only through someone's description of a source was unconfirmable until I reached the source myself.
2. Raise the bar for fluent documents, do not lower it. Polish is now free. A paper that reads like expertise can still be expertise-shaped text wrapped around an error, and arXiv's new ban treats unchecked AI output as a submission-level offense for exactly this reason.
3. Keep confidence tiers explicit and never merge them. A document that blends one reproducible fact, three unchecked claims, and one fabrication into a single confident voice is more dangerous than one that is wrong throughout, because the true fact launders the rest.
4. Hold claims about named people to the highest bar. State only what the primary source supports, and leave everything else explicitly open rather than implied. The cost of getting this wrong about a real person is not symmetric with the cost of getting it right.

The honest summary is narrow, and more useful than a sweeping conclusion would be. Two published papers by one author have serious problems I verified myself from primary sources. A real compute overstatement, a real metric inversion, and a real citation mountain sit alongside a co-author's disputed retraction. None of it required trusting anyone's description of anything; all of it came from files I could open. Trust, but clone the repo.

## Disclaimer

This post discusses real, named researchers and their publicly available work. The problems in the two verified cases are reproducible from primary sources, a public code repository and a public preprint, and are presented as documented errors and undisclosed-AI signatures, not as a legal finding of misconduct.

"Fraud" in the strict sense requires intent to deceive. The reproducible facts here do not by themselves establish intent, and in the regional-statistics case the internal contradictions argue against it. Where a third party has taken formal action, such as the Frontiers retraction, I have quoted that action's own language, including that the investigation did not determine individual involvement and that the authors dispute it.

Claims I could not verify are labeled unverified and are not attached to any individual as fact. If any author can show that an item marked "verified" here is wrong, I will correct it.

## Verification Status of Every Claim

| Claim | Status | How checked |
| --- | --- | --- |
| Monument paper exists, DOI 10.1111/geoj.70073, authors Nicula/Cretan/Dragan/Oancea, Geographical Journal, Feb 2026 | Verified firsthand | Crossref API |
| Repo github.com/bogdanoancea/sentometric holds the code and data, single commit dated 21 Feb 2026 | Verified firsthand | Cloned the repo, read git log |
| Corpus is 61 articles; data runs July 2011 to January 2025; none before 2011; full gap 2017-2020; 21 of 61 from 2023 | Verified firsthand | Parsed monument-data.xlsx |
| Per-monument counts 20 / 15 / 14 / 9; three rows lack date and monument | Verified firsthand | Parsed the dataset |
| Methods section claims a corpus covering "January 1990 to November 2025"; data is July 2011 to January 2025 | Verified firsthand | Read the paper PDF (page 3) and the dataset |
| Script defaults bias sentiment negative via five mechanisms; paper discloses the asymmetry | Verified firsthand | Read monument-analysis.py defaults and logic, and the paper methods (page 5) |
| Score sheet: 9 rows, one monument, mixed data types, same event coded oppositely | Verified firsthand | Inspected the score sheet |
| Page-9 sentence repeated three times (twice back to back); AI declaration names only Grammarly | Verified firsthand | Read pages 9 and 11 of the paper PDF |
| NUTS2 preprint exists, arXiv:2605.02884, sole author Oancea, May 2026 | Verified firsthand | arXiv API and the PDF |
| 4 of 11 flagged regions mislabeled (ES63, ES64, HU11, SK04) | Verified firsthand | Paper's Table 2 vs NUTS 2021 codes (Wikipedia, Data Commons) |
| HU11 is Budapest, narrated as "low education" while its own table shows 55.8 percent, the highest | Verified firsthand | The PDF, Table 2 and discussion |
| "Mahalanobis robust to monotonic transformations" claim is wrong | Verified firsthand | The PDF text plus the definition of the metric |
| 5 percent vs 1 percent threshold asymmetry drives the "less conservative" finding | Verified firsthand | The PDF, methods and Table 1 |
| "First non-missing value" data-handling heuristic | Verified firsthand | Quoted from the PDF methods |
| Prague Economics paper exists, DOI 10.18267/j.pep.898, Oancea/Simionescu/Pospisil, Dec 2025 | Verified firsthand | Crossref API and the open-access PDF |
| Hardware: Xeon Gold 6226R, 16 cores, 192 GB RAM, "more than a week" of compute, on a kilobyte-scale dataset | Verified firsthand | Quoted from the PDF limitations section |
| Test MAPE 0.58 percent below training MAPE 6.98 percent (Romania, monthly table); disclosed, not hidden; not impossible | Verified firsthand | The PDF, Table 18; framed per the math |
| That inversion is proof of data leakage | Unverified | Numbers verified; leakage is one possible cause among others, not established |
| Oancea has about 86,000 citations, bulk from GBD / Lancet consortium papers | Verified | Google Scholar profile, corroborated by University of Bucharest page |
| Simionescu 2021 Frontiers paper retracted 7 Aug 2025 in a network action; authors dispute it; no individual named culpable | Verified | Frontiers notice, Crossref, Retraction Watch |
| Oancea and Pospisil: no PubPeer thread or retraction found | Verified | PubPeer, Crossref/Retraction Watch metadata |
| For Better Science covered the Frontiers retraction and listed Simionescu; not a network expose; does not name Oancea or Pospisil | Verified | The post itself |
| arXiv one-year ban for unchecked AI content, May 2026 | Verified | TechCrunch, Nature, Times Higher Education reporting |

*"Unverified" means I personally lacked access to the primary source, or no underlying data was presented. It is neither a confirmation nor a denial. Confirm before relying on or repeating any unverified item, especially any item that names a person.*

## Sources

Every link below was resolved during the writing of this post. The DOIs were checked against Crossref, the arXiv identifiers against the arXiv API, and the two PDFs (the monument paper and the Prague paper) were read in full.

### The Papers and Their Data (Primary Sources)

- Alexandru-Sabin Nicula, Remus Crețan, Alexandru Dragan, and Bogdan Oancea, ["Computational Analysis of Contested Monuments and Collective Memory in a Multiethnic City"](https://doi.org/10.1111/geoj.70073), *The Geographical Journal* 192 (2026): e70073. DOI: [10.1111/geoj.70073](https://doi.org/10.1111/geoj.70073). Open access.
- Bogdan Oancea, the analysis code and dataset for the paper above: [github.com/bogdanoancea/sentometric](https://github.com/bogdanoancea/sentometric) (`monument-analysis.py`, `monument-data.xlsx`).
- Bogdan Oancea, ["Unsupervised Machine Learning for Detecting Structural Anomalies in European Regional Statistics"](https://arxiv.org/abs/2605.02884), arXiv:2605.02884, 4 May 2026.
- Bogdan Oancea, Mihaela Simionescu, and Richard Pospisil, ["Do Machine Learning Techniques Outperform Autoregressive Distributed Lag Models in Inflation Forecasting?"](https://doi.org/10.18267/j.pep.898), *Prague Economic Papers* 34, no. 4 (2025): 495-558. DOI: [10.18267/j.pep.898](https://doi.org/10.18267/j.pep.898). Open-access [PDF](https://pep.vse.cz/pdfs/pep/2025/04/03.pdf).

### Citation Metrics and the Integrity Record

- Bogdan Oancea, [Google Scholar profile](https://scholar.google.com/citations?user=MHVWyc8AAAAJ) (about 86,000 citations as accessed), corroborated by his [University of Bucharest faculty page](https://unibuc.ro/user/bogdan.oancea/?lang=en) ("over 80,000 citations").
- GBD 2019 Diseases and Injuries Collaborators, ["Global burden of 369 diseases and injuries in 204 countries and territories, 1990-2019"](https://doi.org/10.1016/S0140-6736(20)30925-9), *The Lancet* 396 (2020): 1204-1222. DOI: [10.1016/S0140-6736(20)30925-9](https://doi.org/10.1016/S0140-6736(20)30925-9). The single most-cited item on Oancea's profile, with more than a thousand listed authors; an illustration of hyper-authorship.
- Mihaela Simionescu et al., ["The Impact of Quality of Governance, Renewable Energy and Foreign Direct Investment on Sustainable Development in Cee Countries"](https://www.frontiersin.org/journals/environmental-science/articles/10.3389/fenvs.2021.765927/full), *Frontiers in Environmental Science* (2021), DOI: 10.3389/fenvs.2021.765927, and its [retraction notice](https://www.frontiersin.org/journals/environmental-science/articles/10.3389/fenvs.2025.1675246/full) (DOI: 10.3389/fenvs.2025.1675246, 7 August 2025).
- Retraction Watch, ["Frontiers to retract 122 articles, links thousands in other publishers' journals to 'unethical' network"](https://retractionwatch.com/2025/07/29/frontiers-retract-122-articles-links-thousands-other-publishers-journals-to-unethical-network/), 29 July 2025.
- Leonid Schneider, ["Schneider Shorts 8.08.2025: Increasingly hostile and now defamatory attacks"](https://forbetterscience.com/2025/08/08/schneider-shorts-8-08-2025-increasingly-hostile-and-now-defamatory-attacks/), For Better Science (lists the retracted Simionescu paper; does not name Oancea or Pospisil).
- PubPeer, [comment threads for "Mihaela Simionescu"](https://pubpeer.com/search?q=%22Mihaela+Simionescu%22) (the only one of the three authors with entries).

### Geographic Codes and Methods

- Eurostat, [NUTS classification (NUTS 2021)](https://ec.europa.eu/eurostat/web/nuts/), corroborated for the disputed codes via Wikipedia ([Spain](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Spain), [Hungary](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Hungary), [Slovakia](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Slovakia)) and Google Data Commons. ES63 = Ceuta, ES64 = Melilla, HU11 = Budapest, SK04 = Eastern Slovakia.
- Philip A. Schrodt, [CAMEO: Conflict and Mediation Event Observations, Codebook v1.1b3](https://data.gdeltproject.org/documentation/CAMEO.Manual.1.1b3.pdf) (the event ontology the monument paper uses; this is the URL the paper itself cites).
- C. J. Hutto and Eric Gilbert, ["VADER: A Parsimonious Rule-Based Model for Sentiment Analysis of Social Media Text"](https://ojs.aaai.org/index.php/ICWSM/article/view/14550), ICWSM 2014 (the lexicon the pipeline extends).

### The AI-Content Crackdown and the Reproducibility Backdrop

- Dalmeet Singh Chawla, ["Researchers who use hallucinated references to face arXiv ban"](https://www.nature.com/articles/d41586-026-01595-5), *Nature* news, 19 May 2026. DOI: [10.1038/d41586-026-01595-5](https://doi.org/10.1038/d41586-026-01595-5).
- Devin Coldewey, ["Research repository arXiv will ban authors for a year if they let AI do all the work"](https://techcrunch.com/2026/05/16/research-repository-arxiv-will-ban-authors-for-a-year-if-they-let-ai-do-all-the-work/), TechCrunch, 16 May 2026.
- ["AutoMat": Can Coding Agents Reproduce Findings in Computational Materials Science?](https://arxiv.org/abs/2605.00803), arXiv:2605.00803, 2026.
- SpecKV: ["Adaptive Speculative Decoding with Compression-Aware Gamma Selection"](https://arxiv.org/abs/2605.02888), arXiv:2605.02888, 2026.
- ["Recursive Multi-Agent Systems"](https://arxiv.org/abs/2604.25917), arXiv:2604.25917, 2026.
