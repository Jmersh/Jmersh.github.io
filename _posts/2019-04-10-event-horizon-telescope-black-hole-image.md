---
title: "We Have a Black Hole Image. How Did They Get That?"
date: 2019-04-10 09:00:00 -0400
categories: [Physics, Engineering]
tags: [event-horizon-telescope, black-holes, astrophysics, radio-astronomy, computing]
description: "The first M87 black hole image is here. What interests me is how scattered radio telescopes, precise clocks, and imaging code made it possible."
media_subpath: /assets/img/posts/2019-04-10-event-horizon-telescope-black-hole-image/
image:
  path: cover.png
  alt: "Off-axis black hole ring beside interferometry samples and reconstruction contours"
math: false
mermaid: false
---

There is something ridiculous about finally having [an image of a black hole](https://www.eso.org/public/news/eso1907/). The bright, uneven ring is easy to stare at. Then you remember what it took to get even that much detail.

The Event Horizon Telescope team released the image today. The object is at the center of M87, about 55 million light-years away, with a mass around 6.5 billion Suns. We're seeing radiation from hot material around it and a dark region associated with its shadow. Light isn't coming out of the black hole itself.

The image is also not an ordinary camera exposure. That's the part I want to understand better.

## A telescope with most of the middle missing

The EHT links radio observatories far apart on Earth. Those long distances let it pick out much finer detail than one telescope could on its own. But it doesn't collect everything a solid, Earth-sized dish would collect. The measurements are sparse.

Each site records signals with precise timing. The recordings then have to be brought together, aligned, and calibrated before imaging algorithms can reconstruct a picture. The [ESO announcement](https://www.eso.org/public/news/eso1907/) describes atomic clocks and petabytes of data shipped on hard drives to the processing sites.

I like that detail. An experiment aimed at one of the strangest objects in physics still has a very physical data-transfer problem: get the drives to the computers.

## How do you avoid drawing the ring you expected?

If software has to fill in a picture from incomplete measurements, I'd immediately worry about the assumptions in the software. How much of the result comes from the data, and how much comes from what the algorithm prefers?

The collaboration used different imaging methods and tests on synthetic data to investigate that. The ring and dark center appear across independent reconstructions. The [imaging paper](https://iopscience.iop.org/article/10.3847/2041-8213/ab0e85) goes into the checks in much more detail than the headline can.

That agreement matters to me more than how sharp the final image looks. A prettier reconstruction could easily make me feel more certain without giving me any additional evidence. I want to know which features survive when the method changes.

There's plenty left to ask about the gas, the uneven brightness, and the connection to M87's jet. I'd also like to see how the image changes with more observations. One picture can't settle all of that.

For today, though, a ring that holds up through those checks is enough. It's hard to get over the fact that we can measure structure around an object this distant at all.

## Sources

- ESO, [Astronomers capture first image of a black hole](https://www.eso.org/public/news/eso1907/), April 10, 2019.
- EHT Collaboration, [First M87 results I: The shadow of the supermassive black hole](https://iopscience.iop.org/article/10.3847/2041-8213/ab0ec7), April 10, 2019.
- EHT Collaboration, [First M87 results IV: Imaging the central supermassive black hole](https://iopscience.iop.org/article/10.3847/2041-8213/ab0e85), April 10, 2019.
