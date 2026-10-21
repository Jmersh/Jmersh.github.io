---
title: "Trying to Make Sense of Google's Sycamore Result"
date: 2019-10-23 09:00:00 -0400
categories: [Physics, Computing]
tags: [quantum-computing, sycamore, superconducting-qubits, benchmarking, physics]
description: "Google's Sycamore experiment is impressive, but what did it actually compute? Notes on random circuits, the classical comparison, and IBM's objection."
media_subpath: /assets/img/posts/2019-10-23-sycamore-quantum-random-circuit-sampling/
image:
  path: cover.png
  alt: "Abstract superconducting qubit chip with probability samples and verification lanes"
math: false
mermaid: false
---

Google's [Sycamore result is out today](https://research.google/blog/quantum-supremacy-using-a-programmable-superconducting-processor/), and "quantum supremacy" is a lot of headline to unpack.

The reported comparison is about 200 seconds on the quantum processor versus an estimated 10,000 years on a classical supercomputer. My first question is probably the obvious one: doing what?

It isn't running a normal application faster. The experiment uses 53 working qubits to sample outputs from random quantum circuits. You run a sequence of gates, measure the qubits, and get a string of bits. Repeat that and some strings appear more often than others. The challenge for a classical computer is reproducing that distribution.

So the task is deliberately chosen to be awkward to simulate. That makes sense for testing the hardware, though it means the speedup doesn't tell me how soon a quantum computer will do useful chemistry or solve something I need done.

## The comparison is already being argued over

[IBM has challenged the classical estimate](https://www.ibm.com/quantum/blog/on-quantum-supremacy). Its researchers argue that using disk storage as well as RAM could bring an ideal simulation down to roughly 2.5 days, with higher fidelity. That's their estimate, not a reported completed run of the full simulation.

Two hundred seconds versus a few days is still interesting. But a few days and ten thousand years support very different headlines. The choice of classical algorithm and how it uses the machine are part of the result, not details to sort out afterward.

I don't think this makes the experiment pointless. It does make me wary of treating one benchmark as a clean finish line. The quantum hardware can improve, and so can the software trying to simulate it.

## The checking problem is the interesting bit

There's an awkward question here: if the circuit is too expensive to simulate, how do you know the quantum processor is giving you the right distribution?

Google compares smaller and simplified circuits with classical calculations, checks how the errors behave, and uses that evidence to support the harder runs. That isn't the same as checking every output of the largest experiment against a complete classical answer. Understanding where direct checks stop seems important before deciding how much weight to put on the claim.

I'm impressed that the team can control this many qubits well enough to get a meaningful result through all that noise. I'm also glad the classical comparison is getting challenged. I'd rather see an argument about an actual machine and a specific workload than another vague prediction about quantum computers replacing everything.

For now, I want to know whether the hardware can keep improving as the circuits get larger, and what happens when someone finds a better classical simulation. Both seem more useful to watch than the victory-lap wording.

## Sources

- Google, [Quantum supremacy using a programmable superconducting processor](https://research.google/blog/quantum-supremacy-using-a-programmable-superconducting-processor/), October 23, 2019.
- Arute et al., [The Sycamore paper in Nature](https://www.nature.com/articles/s41586-019-1666-5), October 23, 2019.
- IBM, [On "quantum supremacy"](https://www.ibm.com/quantum/blog/on-quantum-supremacy), October 22, 2019.
