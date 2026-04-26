# Optimal Brain Damage: How to Tell If a Parameter Is Truly Important

> Not all small weights are unimportant, and not all large weights matter.

---

## What is Optimal Brain Damage?

Optimal Brain Damage (OBD) is a classic model pruning method. Its goal is straightforward: remove parameters that are “unimportant” while keeping model performance as intact as possible.

In practice, this often leads to:

- Better generalization (less overfitting)
- Less data required for training
- Faster training and inference

In other words, OBD is about controlling model complexity, not just making the model smaller.

---

## A Common Mistake

A common intuition is:

> Small weights can be safely removed

This assumption is not reliable.

A weight may be small but still critical if the model is highly sensitive to it. Removing such a parameter can cause a large increase in loss. Conversely, a large weight might have little effect on the final outcome.

So the real question is not:

> How large is this parameter?

but:

> What is the cost of removing it?

---

## Saliency: Measuring the Cost of Removal

OBD introduces a key concept:

> Saliency = how much the loss increases if a parameter is removed

This directly answers the question of importance.

---

## Using Taylor Expansion

Directly removing a parameter and retraining to measure loss change is impractical. Instead, OBD approximates this change using a second-order Taylor expansion.

We expand the loss function around the current parameters:

L(w + Δw)

≈ L(w)  
+ first-order term  
+ second-order term  

---

### Key Assumptions

To make the computation tractable, OBD relies on two assumptions:

**1. The model is near a local optimum**

At this point, the gradient is approximately zero, so the first-order term can be ignored.

**2. Parameter interactions are ignored**

Only the diagonal of the Hessian is considered (diagonal approximation), ignoring cross-parameter dependencies.

---

### Final Result

Under these assumptions, we get a simple approximation:

> Saliency ≈ 1/2 × hᵢᵢ × wᵢ²

where:

- wᵢ is the parameter value  
- hᵢᵢ is the diagonal element of the Hessian, representing second-order sensitivity of the loss  

---

## Interpreting the Formula

A parameter’s importance depends on two factors:

**1. Magnitude (w²)**

Larger weights may have a larger potential impact.

**2. Sensitivity (Hessian)**

If a small change in the parameter causes a large change in loss, it is important. Otherwise, it may be safely removed.

The key idea of OBD is combining both factors instead of relying on only one.

---

## Algorithm Procedure

OBD is an iterative process:

Step 1: Train the model  
Step 2: Compute saliency for each parameter  
Step 3: Remove parameters with the smallest saliency  
Step 4: Fine-tune the model  
Step 5: Repeat  

---

## Practical Effects

In practice, OBD can remove a large portion of parameters without significant performance loss:

- More than 60% of parameters can be removed  
- Model performance remains largely unchanged  
- Sometimes performance even improves due to reduced overfitting  

Compared to other methods:

- Magnitude-based pruning: unstable  
- Random pruning: poor performance  
- OBD: more principled and effective  

---

## Core Idea: Balancing Complexity and Error

At its core, OBD addresses a fundamental problem:

> The tradeoff between model complexity and error

Too many parameters lead to overfitting. Too few lead to underfitting. OBD provides a more precise way to reduce complexity.

---

## Why OBD Is Less Common Today

Despite its elegance, OBD has practical limitations.

The main issue is computational cost:

- Computing the Hessian is expensive (typically quadratic complexity)  
- Not feasible for large-scale models  

As a result, simpler alternatives are often used in practice:

- Magnitude pruning  
- Dropout  
- L1 regularization  

These methods are less precise but more scalable.

---

## Summary

OBD offers a fundamental perspective:

> The importance of a parameter is defined by the cost of removing it.

This idea extends beyond neural networks and applies to system design in general.

---

## Further Thoughts

Many modern techniques are, in essence, answering the same question:

How can we reduce complexity without significantly increasing error?

OBD is one of the earliest and clearest answers to this question.