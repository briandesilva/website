---
title : Physics-informed anomaly detection

summary: Overview of my research in the area of physics-informed anomaly detection
date : "2020-04-13"
reading_time : false
share : false
profile : false
comments : false
math : true

---

# The problem

[Anomaly detection](https://en.wikipedia.org/wiki/Anomaly_detection) (also called outlier detection) refers to the process of identifying examples in a dataset that do not exhibit the same patterns as the majority or are otherwise abnormal in some way. Anomaly detection has applications in numerous fields, including fraud detection, aircraft control, fault detection, and machine learning.

My research has focused on a subclass of anomaly detection, called *fault detection*, for sensors measuring quantities in physical systems. Given a stream of sensor readings (a time series) the goal is to determine when one or more of the sensors has started exhibiting atypical behavior or has failed. This should be done as quickly and accurately as possible. For example, one could use a fault detection algorithm to create a warning when a moth has landed on the lens of a security camera, obstructing its view.

Having laid out an objective of sensor fault detection, the next question is how do we actually build a tool to accomplish it.

# A solution
A well-known rule-of-thumb in scientific computing, and science more generally, is the more you know about a problem the more assumptions you can bake into your method for solving it.
The problem we are interested in is fault detection in sensors taking measurements of some physical system (e.g. sensors on an airplane or car). And the property we will exploit is the existence of the underlying physical system. The presence of this system will often induce correlations between different sensors (e.g. the odometer reading of a car is probably related to the air resistance the car experiences).

Our approach for detecting sensor faults involves training a physics model to characterize the interactions between sensors *when all sensors are behaving normally*. (The process of finding this model is sometimes referred to as system identification.) Given a constant stream of sensor measurements, we can then compare the output of the physics model with the sensor readings, flagging any large discrepancies as potential failure events.

Furthermore, we will attempt to do all of this in an *automated* fashion. We want to be able to feed in labeled training data and receive a fully-trained model that is ready to identify sensor failures. We will go into more detail about how all this is accomplished in the following sections.

**High-level ideas:**
- Train a model that can predict how the system should evolve.
- Use the model to detect when something has gone wrong (e.g. a sensor has failed).

## Kalman filters
[Kalman filters](https://en.wikipedia.org/wiki/Kalman_filter) are a popular tool for fault detection because they are simple, widely-applicable, fast, and robust. A simple Kalman filter "observer" system looks like the following:

$$
	\hat{x}\_{k+1} = Ax\_k + By\_k + K(x\_k - \hat{x}\_k).
$$

- $x\_k$ is a vector of the *measurements of interest* (the sensor values for which we are interested in predicting faults) at time $t\_k$.
- $\hat{x}\_k$ is the Kalman filter's estimate of the measurements of interest at time $t\_k$
- $y\_k$ is the set of sensor measurements which are potentially relevant. We are not interested in detecting failures in these sensors, but we may want to include them in the model if their outputs are correlated with $x\_k$.
- $A$ and $B$ are matrices helping to predict $\hat{x}\_{k+1}$ from $x\_k$ and $y\_k$. They encode the relationships between the different sensors.
- $K$ is the Kalman gain; a matrix controlling the influence of the smoothing effect of the innovation $(\hat{x}\_k - x\_k)$. The $K$ giving optimal convergence of the estimate to the true state can be computed analytically.

This system simply maintains an estimate $\hat{x}\_k$ of the measurements of interest $x\_k$ and evolves it in time based on the observed values (sensor readings) $x\_k$ and $y\_k$. We can compare the prediction for the state at the next timestep $\hat{x}\_{k+1}$ with the state we actually observe $x\_{k+1}$ to determine when a sensor has failed.

This system is an example of a *linear time-invariant* (LTI) system because $A$, $B$, and $K$ do not depend on time. Speaking of $A$ and $B$, how do we compute them? For simple enough problems we may have explicit knowledge of how the different sensors relate to one another, but typically we will not be so lucky. The Kalman filter model assumes we already have an LTI system (i.e. that we already have $A$ and $B$), and does not give us a way to construct them. Fortunately, there are data-driven ways to do so.

## Obtaining an LTI model
The [dynamic mode decomposition](https://en.wikipedia.org/wiki/Dynamic_mode_decomposition) (DMD) gives us a way to *automatically* find an LTI system involving the measurements of interest.

In particular, given measurements $\\{x\_1, x\_2, \dots, x\_{m+1}\\}$, DMD finds a fixed matrix $A$ satisfying
$$ x\_{k+1} \approx A x\_k\quad \forall k \in \\{1,2,\dots, m\\}.$$

In words, DMD finds the best linear dynamical system (or map, in the discrete case) to describe the evolution of the data. If we want to incorporate auxiliary measurements $y\_k$, we will need to use dynamic mode decomposition with control (DMDc). Given measurements $\\{x\_1, x\_2, \dots, x\_{m+1}\\}$ and $\\{y\_1, y\_2, \dots, y\_{m+1}\\}$, DMDc finds both $A$ and $B$ satisfying
$$x\_{k+1} \approx A x\_k + B y\_k\quad \forall k \in \\{1,2,\dots, m\\}.$$

## Putting it all together
With all the pieces of our Kalman filter model in place we are almost ready to begin predicting sensor failures. The last missing ingredient we need is some sort of decision-making process to decide when the innovation $x\_k - \hat{x}\_k$ has grown too large in magnitude. A threshold-based rule might suffice in simple applications, but in general, a classifier is probably better-suited to the task. One benefit of using a more general-purpose classifier is it can make use of other features besides just the innovation (e.g. the current or past sensor measurements). In the applications on which I have worked a decision tree has proved to be sufficient.

The overall (offline) training process for the model is as follows:
1. **Learn an LTI model** via DMDc. This requires a time series of normal measurements (one with no sensor failures). The Kalman gain $K$ for the Kalman filter can either be computed analytically (by solving a Riccati equation) or empirically (via cross-validation in the following step). 
2. **Train a classifier** to predict when sensor failures have occurred. This step requires labeled training data in the form of sensor measurements along with labels indicating when different sensors have failed. It can be useful to include multiple different manifestations of sensor faults in this set. Hyperparameters for the classifier can be selected with cross-validation.

Given a streaming series of measurements $x\_1, x\_2,\dots, x\_k$ and $y\_1, y\_2,\dots, y\_k$ in an online setting, the ...
1. Use the Kalman filter to estimate the state $\hat{x}\_k$ and compute the difference between the estimated and measured states $x\_k - \hat{x}\_k$ (the innovation).
2. Feed the innovation and other sensor measurements $x\_k$ and $y\_k$ into the classifier to obtain a prediction for whether any sensors have failed.

## Extensions
* A linear model may not be flexible enough to reproduce complicated dynamics. In such cases one can introduce time-delay versions of the measurements to increase the capacity of the LTI by effectively adding nonlinearities to the model. See the HAVOK reference below for more information.
* For noisy datasets the model's precision can be improved with little impact to its recall (many fewer false positives at the cost of a minimal number of additional false negatives) by working with a moving-window average version of the innovation. This serves to make the overall algorithm less "jumpy" by smoothing out the high-frequency signals that are often caused by noise.

I am currently working on a paper with some collaborators wherein we apply this method to a variety of airplane flight datasets.


# Resources
* [An introduction to the Kalman filter](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.336.5576&rep=rep1&type=pdf)
* [Dynamic mode decomposition with control](https://epubs.siam.org/doi/abs/10.1137/15M1013857)
* [Hankel alternative view of Koopman (HAVOK) analysis](https://www.nature.com/articles/s41467-017-00030-8) (time delay embeddings)