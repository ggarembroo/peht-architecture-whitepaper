# PEHT Architecture Whitepaper

Defensive publication. 2026-10-15. Egor Pobozhii.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23387609.svg)](https://doi.org/10.5281/zenodo.23387609)

Three techniques that a two-phone ternary cluster can demonstrate today, plus eleven more
vectors for hardware-coupled inference. Published as prior art: nobody can patent these
concepts after this date, and everybody can build on them.

## The flagships

**1. PE-LZT, Logits Zero-Transmission (993 KB per token becomes about 5 KB).**
The wire carries hidden states, never the vocabulary. Both ends compute argmax locally and
reconcile float-level disagreement with a measured threshold and rare tiny packets. The
vocabulary stops being part of the transport.

**2. Time quantization. Optimal speculative depth as a theorem.**
D* = floor(ln(e_step * gamma / C_r) / ln(a)). At our measured acceptance 0.531 the optimum
is exactly 4, at 0.7 it is 8. Idle cycles of the second phone buy future tokens; branches
run as futures priced in wire bytes.

**3. PE-VCA, Video-Codec Architecture. The codec as an AI coprocessor.**
Motion-estimation SAD arrays, ISP convolvers and display pipelines idle 95 to 99 percent of
their lives, and their native primitives (SAD, convolution, bitwise ops) are exactly the
shapes ternary arithmetic lives in. First instrumented, then exploited.

**4. Pobozhii's Law (D* Theorem).** The optimal speculative depth
D* = floor(ln(e_step * gamma / C_r) / ln a). At acceptance 0.531 and a step priced at
5 percent of a round, D* = 4, the observed optimum. Derived in Volume I, vector 4.

## Layout

- `PEHT-Architecture-Whitepaper.md` the full document, Volume I and II
- `ENGINEERING-FIRSTS.md` engineering firsts, release 1
- `whitepaper.sha256` the digest of the released revision
- `whitepaper.ots` OpenTimestamps proof, anchored in Bitcoin
- `CITATION.cff` machine-readable citation

## Prior art notice

Every concept in this repository is published as prior art. Copyright (c) 2026 Egor Pobozhii.
The text is licensed CC BY 4.0. Copyright covers the text only; the ideas are given to the
public on purpose, so that no one can fence them off.
