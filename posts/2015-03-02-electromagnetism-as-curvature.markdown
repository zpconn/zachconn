---
title: Electromagnetism as Curvature
---

One of the greatest and most shocking advances of physics occurred around 1916 when Einstein proposed his general theory of relativity. In this theory he proposed that gravity might not be action at a distance, as Newton supposed, but rather might result from massive bodies simply following "straight lines" in a curved space. This is rightfully viewed as one of the most beautiful insights of all time. Nobel laureate Max Born called it the "greatest feat of human thinking about nature."

It turns out that other interactions in nature can also be understood in terms of curvature. Hermann Weil attempted this, introducing what is now called gauge theory, though his initial attempt was fatally flawed (and the flaw was indeed discovered by Einstein himself). The name "gauge theory" has stuck, though, even though it no longer seems related to the body of techniques which it describes.

It is assumed that you know some differential geometry and topology, including fiber bundles, but I assume you know nothing about what a "gauge" is or what "gauge invariance" might mean. Some passing familiarity with quantum mechanics is also assumed; you mostly just need to understand wavefunctions. There's a sense in which this post might represent "the gauge theory of electromagnetism for mathematicians," except I'm going to keep formulae to a minimum because I'm mostly interested only in the high-level picture.

Recall that in quantum mechanics one describes a particle or a system by means of a mathematical entity known as a wavefunction. This wavefunction is simply a function assigning a complex number to every point of spacetime. For us, spacetime is four-dimensional Minkowksi spacetime, assumed to be flat.

In the case that there is only one spatial dimension, this is easy to visualize. Space is represented by an infinite line, and the complex numbers assigned to points in space by the wavefunction can be seen as vectors attached and orthogonal to this line, potentially wrapping around it in various oscillatory patterns. A momentum state, for instance, would look like a corkscrew.

With three spatial dimensions, we can't easy visualize the resulting geometric object which represents both space, time, and the complex geometry of the wavefunction. We know exactly what sort of object it is, though. It's a **fiber bundle**: the base space is 4D Minkowski spacetime and the fibers are copies of the complex plane. Thus we can say that a wavefunction is actually a continuous section of a complex line bundle over spacetime.

We know from quantum theory that one can multiply a wavefunction by a fixed complex phase (that is, a complex number of unit modulus) without affecting the physical meaning of the wavefunction. This is because the magnitude of the wavefunction at each point represents the probability of a measurement finding the particle to reside at that particular point, and multiplication by a number of unit length clearly does not change this probability. Thus there exists a **global symmetry** whereby we may rotate the entire wavefunction by a fixed complex phase, and the result must represent the same state.

Let us consider what happens when we promote this to a *local* symmetry, also known as a **gauge symmetry**. This means that we allow the complex phase to vary continuously across different points of spacetime. We run into issues here; the result is *not* necessarily the same physically.

What is the issue, precisely? It's a familiar one from differential geometry. Because the phase now varies from point to point, we no longer know how to compare the value of the wavefunction at one point to its value at a nearby point. Intuitively, this is related to the notion of *parallel transport*; we don't know anymore how to properly parallel transport a vector above one base point of the bundle to another fiber above a nearby base point.

Suppose we introduce a modified version of parallel transport which takes into account the differing phases. As we know, this introduces an affine connection on the bundle which possesses nontrivial curvature. In this new picture, a wavefunction becomes a section of a complex line bundle with structure group U(1); recall that U(1) is the unit circle in the complex plane, that is, the group of unit modulus complex numbers under multiplication. The Riemann curvature tensor of the bundle connection which arises in this way is the electromagnetic field tensor. All of electromagnetism can be (in theory) derived from this point.

By the way, at this point the conservation of electric charge in spacetime can also be deduced fairly quickly. Specifically, one observes that the Lie algebra of the continuous symmetry group U(1) is one-dimensional. It follows from a version of Noether's theorem that the generator of this algebra leads to a conserved quantity: electric charge.

