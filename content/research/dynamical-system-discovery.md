---
title : Data-driven discovery of dynamical systems

summary: Overview of my research in the area of data-driven discovery of dynamical systems
date : "2019-12-17"
reading_time : false
share : false
profile : false
comments : false

---
# The problem
A large chunk of applied mathematics aims to understand dynamical systems and to characterize their behavior.

But what happens if you have a physical system for which you don't *know* the governing equations?

You might suspect that there is a set of differential equations pulling the strings, but maybe you are unable or unwilling to derive them analytically.

If you can get your hands on *measurements* of the system, then you may be able to use a data-driven approach.

# A solution
One recently developed method for nonlinear model discovery is the Sparse Identification of Nonlinear Dynamical systems (SINDy).

## How it works
The main idea is that the right-hand sides of many dynamical systems of interest do not include many terms, implying that they are *sparse* with respect to an appropriately chosen basis.

![Image summary of the SINDy method](/img/SINDY_fig.png)

1. Measurement data is passed in (one can optionally feed in derivatives of the measurements as well)
2. A library of possible interaction functions is specified. If this set of functions is rich enough then a subset of them will make up all the terms on the right-hand side of the dynamical system to be uncovered.
3. Sparse regression is used to identify which terms best reproduce the derivatives of the measurement data, providing an approximation to the underlying dynamical system.

# Resources
* The original paper introducing SINDy: [Discovering governing equations from data by sparse identification of nonlinear dynamical systems](https://www.pnas.org/content/pnas/113/15/3932.full.pdf)
* A recent paper of mine applying SINDy to a noisy real-world data set: [Discovery of physics from data: universal laws and discrepancies](https://arxiv.org/abs/1906.07906)
* [PySINDy](https://github.com/dynamicslab/pysindy): a Python package for SINDy
