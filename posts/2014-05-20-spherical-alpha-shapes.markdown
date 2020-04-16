---
title: 'Data visualization: spherical alpha shapes'
---

Suppose you are given a set of points on a surface, say a flat sheet of paper or a sphere. Your goal is to find a bounding polygon with the following properties:

1. The vertices of your polygon are a subset of the original set of points.
2. Every point is contained in the polygon.
3. The polygon, in some sense, characterizes the "shape" formed by the points on the surface.

Of course, it is the third condition that makes this problem interesting. There isn't one way to define what a characteristic shape might look like, and even if you do decide on a rigorous definition this is the sort of problem that can lead you down the rabbit hole of increasingly obtuse computational geometry algorithms.

A naive solution which still might be acceptable in some situations would be to simply find the convex hull. There are well-known ways of doing this pretty efficiently (in \\(O(N \\log(N))\\) time, for \\(N\\) points). But what if the points are uniformly distributed in a donut shape? A convex hull cannot detect the hole and therefore might be completely unsatisfactory depending on the application.

What we need is a "concave hull." Such a thing is not well-defined, though. Indeed, if we're not careful, we might even accidentally include the minimal spanning tree in our set of possible hulls, as would happen if we sought a polygon of minimal area, for instance.

Enter alpha shapes. For a given point set there corresponds a one-parameter family of bounding polygons. The parameter, α, which is a nonnegative real number, determines how "tight" the polygon is. Depending on the exact definition used, increasing or decreasing α causes the polygon to tend between two archetypes: the convex hull at one extreme and a spanning tree at the other.

The formal definition of an alpha shape is quite interesting, but we're not going to use it, because we don't need it. For us it is sufficient to know that an alpha shape is always a subcomplex of the Delaunay triangulation of the convex hull of the set of points. If we know how to compute this triangulation, then we can find an alpha shape with only two extra steps:

1. Delete any triangles from the triangulation which are "too big," a condition I define in the following very simple way. A triangle is "too big" if any of its edges is longer than α (some people, by convention, might prefer \\(1/\\alpha\\) here).
2. What results is a triangulation of the alpha shape. We no longer need the interior geometry, so we merge the triangles to obtain just the bounding path.

This is simple enough if our points reside in a plane. It gets even more interesting if our points are on a sphere (say the locations of all worldwide cities). In this case, we need the spherical Delaunay triangulation. A beautiful observation is that this is equivalent to the three-dimensional convex hull of the set of points in 3-space. With this in mind, the only other modification necessary is of course to use the spherical distance metric.

The following example shows an implementation of alpha shapes using these ideas in sherical geometry using D3.js: [http://bl.ocks.org/zpconn/11387143](http://bl.ocks.org/zpconn/11387143). The final merge step is performed using TopoJSON.
