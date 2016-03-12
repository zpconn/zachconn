---
title: Beautiful problems: the first example
---

The following problem appeared on the 2016 Team Selection Test as part of the process of identifying the contestants who will represent the United States in the IMO (International Math Olympiad).

#### Problem

Let \\(p\\) be a prime number, and let \\(\\mathbb{F}_p\\) represent the finite field of integers modulo \\(p\\). Let \\(\\mathbb{F}_p[x]\\) be the set of polynomials with coefficients drawn from \\(\\mathbb{F}_p\\), and define the function \\(\\Phi : \\mathbb{F}_p[x]\\rightarrow \\mathbb{F}_p[x]\\) via


\\[\\Phi(P) = \\sum_{i=0}^n a_i x\^{p^i}\\]

whenever \\(P(x) = \\sum_{i=0}^n a_i x^i\\). Prove that for all nonzero polynomials \\(F,G\\in \\mathbb{F}_p[x]\\), one has \\(\\Phi(\\text{gcd}(F,G)) = \\text{gcd}(\\Phi(F),\\Phi(G))\\). (The gcd of two polynomials \\(F,G\\) is the polynomial of greatest degree that divides both \\(F\\) and \\(G\\).)

#### Solution

The solution presented here relies on the technique of invariants. Specifically, we present a special operation that can only be applied a finite number of times and which keeps a certain equality invariant throughout all its applications. This produces a finite chain of equalities which collapse to the desired result.

We make a few preliminary observations that follow from the familiar identity \\((a+b)\^p = a^p+b\^p\\) which holds in the field of \\(p\\) elements. Specifically, one has \\(\\Phi(xP) = \\Phi(P)\^p\\) and \\(\\Phi(F+G)=\\Phi(F)+\\Phi(G)\\).

Suppose \\(F,G\\in \\mathbb{F}_p[x]\\) are distinct monic polynomials, and suppose without loss of generality that \\(\\text{deg}(F) \\leq \\text{deg}(G)\\). Define a **reduction** on the pair \\((F,G)\\) to be the following operation:

1. Compute \\(c = \\text{deg}(G) - \\text{deg}(F)\\).
2. Replace the pair \\((F,G)\\) with the new pair \\((F,G - x^c F)\\).
3. Make each polynomial in the new pair monic by dividing out by its leading coefficient.

Observe that a reduction maintains the gcd of the pair both before and after the operation. Moreover, the process can only be applied finitely many times until it converges on the final pair \\((D,D)\\) where \\(D=\\text{gcd}(F,G)\\).

Define the \\(\\Phi\\)-**pair** of the pair \\((F,G)\\) to be \\((\\Phi(F),\\Phi(G))\\). A reduction on \\((F,G)\\) sends its \\(\\Phi\\)-pair to the \\(\\Phi\\)-pair of \\((F,G-x^c F)\\), specifically \\((\\Phi(F), \\Phi(G) - \\Phi(F)\^{p^c})\\) thanks to the properties of \\(\\Phi\\) mentioned above. Thus the gcd of the corresponding \\(\\Phi\\)-pair is preserved as well.

We run the reduction process for \\((F,G)\\) and aso take note of the sequence of \\(\\Phi\\)-pairs thus produced. Observe that both sequences terminate at the pairs \\((D,D)\\) and \\((D',D')\\), respectively, where \\(D=\\text{gcd}(F,G)\\) and \\(D' = \\Phi(D)\\). Thus, 

\\[\\text{gcd}(\\Phi(F),\\Phi(G)) = \\Phi(D) = \\Phi(\\text{gcd}(F,G)),\\]

as desired.
