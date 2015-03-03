---
title: Electromagnetism as Curvature
---

I've recently discovered a piece of modern theoretical physics which really struck me with its beauty and depth, and I wanted to write a post on it.

One of the greatest and most shocking advances of physics occurred around 1916 when Einstein proposed his general theory of relativity. In this theory he proposed that gravity might not be action at a distance, as Newton supposed, but rather might result from massive bodies simply following "straight lines" in a curved space. This is rightfully viewed as one of the most beautiful insights of all time. Nobel laureate Max Born called it the "greatest feat of human thinking about nature."

As far as curvature in physics goes, I thought this was the end of the story. Gravity is curvature. The other forces are, well, just other things. But it turns out I wasn't quite right. There is a framework in which other interactions of nature, such as electromagnetism, are also manifested as curvature (or are at least related to it). This is generally known as gauge theory, and it's actually quite a bit more sophisticated than anything appearing in general relativity. Nevertheless I hope to provide a decent introduction to the geometric and topological ideas of gauge theory in this post. This won't teach you how to compute anything (as I scarcely know how to do that myself); I hope only to convey a high-level idea about how something like electromagnetism might *also* be curvature.

I'm going to assume a fairly high level of mathematical acumen here, but I also want to develop a fair amount of the material from scratch. Thus it is assumed that you know some differential geometry and topology, including fiber bundles, but I assume you know nothing about what a "gauge" is or what "gauge invariance" might mean. Some passing familiarity with quantum mechanics is also assumed; you mostly just need to understand wavefunctions. There's a sense in which this post might represent "the gauge theory of electromagnetism for mathematicians," except I'm going to keep formulae to a minimum because I'm mostly interested only in the high-level picture.

Recall that in quantum mechanics one describes a particle or a system by means of a mathematical entity known as a wavefunction. This wavefunction is simply a function assigning a complex number to every point of spacetime. For us, spacetime is four-dimensional Minkowksi spacetime, assumed to be flat.

In the case that there is only one spatial dimension, this is easy to visualize. Space is represented by an infinite line, and the complex numbers assigned to points in space by the wavefunction can be seen as vectors attached and orthogonal to this line, potentially wrapping around it in various oscillatory patterns. A momentum state, for instance, would look like a corkscrew.

With three spatial dimensions, we can't easy visualize the resulting geometric object which represents both space, time, and the complex geometry of the wavefunction. We know exactly what sort of object it is, though. It's a **fiber bundle**: the base space is 4D Minkowski spacetime and the fibers are copies of the complex plane. Thus we can say that a wavefunction is actually a complex line bundle over spacetime.

We know from quantum theory that one can multiply a wavefunction by a fixed complex phase (that is, a complex number of unit modulus) without affecting the physical meaning of the wavefunction. This is because the magnitude of the wavefunction at each point represents the probability of a measurement finding the particle to reside at that particular point, and multiplication by a number of unit length clearly does not change this probability. Thus there exists a **global symmetry** whereby we may rotate the entire wavefunction by a fixed complex phase, and the result must represent the same state.

Let us consider what happens when we promote this to a *local* symmetry, also known as a **gauge symmetry**. This means that we allow the complex phase to vary continuously across different points of spacetime. We run into issues here; the result is *not* necessarily the same physically.

What is the issue, precisely? It's a familiar one from differential geometry. Because the phase now varies from point to point, we no longer know how to compare the value of the wavefunction at one point to its value at a nearby point. Intuitively, this is related to the notion of *parallel transport*; we don't know anymore how to properly parallel transport a vector above one base point of the bundle to another fiber above a nearby base point.

Suppose we introduce a modified version of parallel transport which takes into account the differing phases. As we know, this introduces an affine connection on the bundle which possesses nontrivial curvature. In this new picture, a wavefunction becomes a complex line bundle with structure group U(1); recall that U(1) is the unit circle in the complex plane, that is, the group of unit modulus complex numbers under multiplication. The curvature tensor of the bundle connection which arises in this way is, almost unbelievably, the electromagnetic field tensor. Thus, in making the wavefunction invariant under this family of local gauge transformations, we have introduced the phenomenon of electromagnetism. 

I hope this is as shocking to you as it was to me. I can't explain it other than to say that this edifice of physics and mathematics leaves me in awe, mouth agape.

