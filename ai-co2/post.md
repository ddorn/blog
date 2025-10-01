We all—policymakers, organisations, researchers, and individuals—need a reliable way to measure the environmental impact of AI usage. It is essential for developing evidence-based policies, investing in effective environmental measures, optimising LLM inference stacks, and for enabling individuals and organisations to understand the environmental impact of their actions and act accordingly.

But measuring the impact of a given interaction with an LLM is not straightforward, and simple metrics usually fail to account for important parameters. Indeed, the environmental impact of a specific query depends on its length, the length of the response, modalities (image, sound, video), model size and quantisation, hardware utilisation, the energy mix of the servers, and many other interacting variables… On top of this, for efficiency, many queries are usually batched together and processed at the same time on the same machine, so we have only access to summary statistics.

A good starting point to propose an answer is Google's recent paper "[Measuring the environmental impact of delivering AI at Google Scale](https://arxiv.org/abs/2508.15734)" (2025, August 21). They measured emissions across their whole fleet of servers running the Gemini apps, and despite not being a proper lifecycle assessment, included more sources of emissions than many studies. From this, they report two main numbers: the median prompt on Gemini apps uses 0.24 Wh, equivalent to 0.03g CO₂e.[^water-use]

The usefulness of those numbers depends entirely on *what is a 'median prompt'* and how stable and representative it is as a unit. Scalar metrics are great, because they are easy to share and understand, but there are a few properties of prompts that make scalar metrics hard to develop well.

**Prompt size.** Prompts can vary in size by at least 3 orders of magnitude. From a quick "hi!" using few thousand tokens[^system-prompt-2800] to "translate this whole book to French" using up to a million tokens (the maximum context window of Gemini 2.5), emissions depend strongly on the prompt length and on the length of its answer. This is exacerbated by the relationship between prompt lengths and emission being non-linear (likely quadratic, due to the attention algorithm), meaning that a 4x longer prompt would use more than 4x the energy. This makes it crucial to specify the length of a prompt and its answer, as has been done for instance in the [LCA of Mistral Large 2](https://mistral.ai/news/our-contribution-to-a-global-environmental-standard-for-ai), which considers only 400-token responses.

**Fixed model.** The impact of a prompt also depends greatly on the LLM queried, as different models have very different cost profiles—from 0.40$ for 1M output tokens of Gemini 2.5 flash lite to up to 15$/Mtok for Gemini 2.5 pro. With almost 2 orders of magnitude of difference in price, the emissions of those models likely vary by at least one order of magnitude.[^price-is-market] Thus, one cannot merge the use of very different models in one metric, and must report numbers for each model separately.

**Dealing with heavy-tailed distributions.** Given the many orders of magnitude a prompt's emissions can take, they likely follow some heavy-tailed distribution: most emissions are small but a non-negligible amount are much higher. This kind of distribution is hard to summarise to a single number.

The median is usually very misleading as it hides the impact of the most expensive queries, which likely account for most of the impact of LLM use. This is critical if aggregating multiple models at once: if small models serve 60% of the requests and large models 40%, the emission reported would be that of the 50th percentile, which is a small model, and completely ignore the emissions of the larger ones, even if they were 5x larger and represented most of the emissions.[^median-eu-flights]

The median is also much lower than the average, but the average is as about as misleading, as it is too sensitive to outliers[^average-musk-wealth], doesn't represent well the bulk of lower-impact queries, and they both hide important properties of the distribution.

The best solution for this is to *communicate the full distribution*. Using the median discards the most energy-intensive half of the generations, which are much more energy-intensive than the bottom half and are the generations at the core of the challenge of energy optimisation.

Even with a proper methodology on _how_ to count emissions, there are also a lot of choices on _what_ to count.

**Location-based emissions.** Emissions should be reported using location-based emissions, and not market-based emissions. The location-based emissions correspond to the carbon emitted by the datacenters, which is the main figure of interest. On the other hand, market-based emissions are an accountability figure. They correspond to the location-based emissions minus the amount of clean energy certificates bought by a company. Those clean energy certificates are comparable to independent investments in renewable energy, and cannot be meaningfully removed from the datacenter's emissions. For instance, if Google had bought twice as many certificates, they could have concluded that their LLM apps have negative emissions, and actually remove carbon from the atmosphere!

**Decisions inform metrics.** Now, if there's one thing I've learned from reading "[How to Measure Anything](https://www.goodreads.com/book/show/444653.How_to_Measure_Anything)", it's that the goal of measuring is to enable better decision-making, and so we should design our metrics based on which decisions we need to make.

**Striving to be better.** To have an honest conversation about the environmental impact of LLM use, future papers will need to:

1.  Report emissions separately for each model
2.  When giving per-prompt estimates, report the prompt and completion length
3.  Or better: report emissions for multiple or all prompt and answer lengths
4.  Even better: report emissions for every usage percentile, not only the 50th (along with the prompt lengths at each percentile)
5.  Always use location-based emissions to report about usage emissions

**Science is collaborative.** These methodological choices have a significant, cumulative effect on a paper's conclusions. They also highlight the importance of the peer-review process, which is designed specifically to challenge these kinds of methodological assumptions before publication. An external reviewer of this paper would almost certainly have questioned whether the 'median prompt' is a robust metric or why the costs of high-end models were not disaggregated.

**Even better.** If I had the time and access, I would try to provide a **predictive model** of per-prompt emission. It isn't possible to measure directly the power used by a single prompt because of batching, but one can surely model it using the coarse estimates of power consumption of a group of prompts.

The relationship between prompt length, model, modalities, and emissions is surely piecewise linear or quadratic, and LLM providers have more than enough data to fit such a model. I would then create a small page where users can see how much emissions a specific prompt would likely produce, and even connect their Gemini account to get detailed reports. This is empowering transparency.

## Footnotes

[^water-use]: They also have a section on water use, but I won't focus on it.

[^system-prompt-2800]: Every interaction in the app includes a system prompt, which [currently is 2800 tokens](https://github.com/elder-plinius/CL4R1T4S/blob/81ea2ad8a9199df110b8ec2773c93a75e1d55d19/GOOGLE/Gemini-2.5-Pro-04-18-2025.md).

[^price-is-market]: Price is an indicator of the market more than resource use, but it is reasonable to expect it to be correlated, given that it correlates with expected model sizes.

[^median-eu-flights]: It's similar to saying that the median EU citizen produced 0g of CO₂ from airplane emissions. It's possibly true, important to have in mind when discussing the impact of planes, but tells you very little about the side of the distribution we care about!

[^average-musk-wealth]: The average wealth of people in a kilometre radius around Elon Musk is > 1 million dollar.