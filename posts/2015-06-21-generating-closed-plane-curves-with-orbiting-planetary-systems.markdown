---
title: Generating closed plane curves with orbiting planetary systems
---

There is a remarkable result in mathematics which says that given a sufficiently well-behaved closed curve in the plane one may assemble a system of orbiting planets, each orbiting the previous planet in the sequence, such that the motion of the final planet precisely traces out the given curve. Even though this statement is equivalent to famous results in Fourier theory, it is very rarely appreciated in this highly geometric form. Here I'd like to briefly sketch both why this statement might be true and also suggest a method for numerically approximating the orbiting planetary system given a finite sequence of points on the curve.

First, the key assumption here is that the orbits are perfectly circular. This is a clear departure from actual planetary orbits, which often are not even perfectly elliptical due to both the effects of different planets on each other but also general relativity (which leads to, e.g., the precession of Mercury's orbit).

Hinting at why the result might be true is as simple as writing down what it means to have a chain of planets, each orbiting the previous one. To simplify the notation, it's ideal to work in the complex plane. Recall that from Euler's formula we can express circular motion in the complex plane via the beautiful parameterization \\(z(t) = re^{2\\pi imt}\\). This describes a particle orbiting the origin counter-clockwise along a circle of radius \\(r\\). It orbits at the rate of \\(m\\) revolutions per unit time, i.e., when \\(t = 1/m\\) it will have produced a full revolution and thus will produce \\(m\\) full revolutions by the time that \\(t=1\\).

What if we have a second planet which is orbiting the one we just described, with a new radius \\(r_2\\) and a new rate \\(m_2\\)? In fact, its motion \\(z_2(t)\\) is simply the sum

\\[z_2(t) = z(t) + r_2e^{2\\pi i m_2 t} = re^{2\\pi imt} + r_2e^{2\\pi im_2 t}.\\]

If you're not convinced that it's this simple, recall that addition of complex numbers is just vector addition. So we take the first point in the sum, which is the location of the first planet, and we simply add to it the displacement vector from the first to the second orbiting planet.

It's clear that in general we will find a sum of the form

\\[z(t) = \\sum_{n=-\\infty}^{\\infty} r_n e^{2\\pi i n t}.\\]

We can reproduce the above by considering cases where only finitely many of the coefficients \\(r_n\\) are nonzero. What's remarkable here is that this is nothing but a Fourier series, which means standard theorems of Fourier analysis apply. Indeed, with light assumptions on the curve \\(z(t)\\) such a representation will always exist, and we can explicitly compute the coefficients via the formula

\\[r_n = \\int_0^1 e^{-2\\pi i n t}z(t)\\, dt.\\]

What if we only know some sample points on the curve, say \\(z_0,z_1,\\dots,z_{N-1}\\)? Can we approximate these integrals and thereby construct an approximate planetary system that will try to pass the final planet through the given points? In fact we can. Of course there is loss of information, but applying, say, the trapezoidal rule for the integral we find the approximation

\\[r_n = \\frac{1}{N}\\sum_{k=0}^{N-1} z_k e^{-2\\pi ink/N}.\\]

Note that these coefficients are generally nonreal, but our geometric interpretation requires them to be real. We can resolve this issue by simply breaking each term \\(r_n e^{2\\pi i n t}\\) into two terms. Indeed, if \\(r_n = a_n + b_n i\\), then we simply write

\\[r_n e^{2\\pi i n t} = a_n e^{2\\pi i n t} + b_ne^{i(2\\pi nt + \\pi/2)}.\\]

Thus we obtain two orbits, one of which starts off at a shift of \\(\\pi/2\\) radians compared to the other. These orbiting planets can be arranged in either order thanks to the commutativity of addition.

Alternatively, we can define the \\(n\\)th harmonic, for \\(n \\geq 0\\), via \\(h_0(t) = z_0\\) and

\\[h_n(t) = r_n e^{2\\pi int} + r_{-n} e^{-2\\pi int}.\\]

The curve is then a sum of harmonics, and each harmonic is not a circular orbit but an elliptical one with semi-major axis \\(\\left|r_n\\right| + \\left|r_{-n}\\right|\\).

An implementation of this approximation in [Elm](https://elm-lang.org/) is available [here](https://github.com/zpconn/elm-fourier).
