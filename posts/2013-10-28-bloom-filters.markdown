---
title: Must-Know Algorithms (Part 1): Bloom Filters
---

This is Part 1 of a series covering slightly obscure data structures and algorithms which I nevertheless believe every professional software engineer should know about. These algorithms are chosen both for their underlying elegance and simplicity but also for their practicality. You may not use them daily, but when you do they can be life-savers.

In this first part, I will discuss an incredibly simple but powerful structure for approximately answering the question of set membership very quickly with minimal memory overhead. The structure, called a ***bloom filter***, is little more than a glorified bit vector, and it guarantees no false negatives (i.e., if it tells you that an element is not in the set, then you can know for sure that the element is definitely not in the set). The trade-off is that it can produce false positives. A good use-case for a bloom filter, therefore, is a situation where you want a simple way to potentially rule out a much more expensive computation.

Formally, a bloom filter is just a bit vector of length ```m``` together with a family of ```k``` indendent hash functions mapping elements of your set onto nonnegative integers strictly less than ```m```. To construct the filter, simply loop over the set and, for each object, calculate the ```k``` independent hash indices and set the corresponding bits in the vector if they aren't already set.

To answer the question of set membership, given a candidate element, again compute its ```k``` hash indices. If every corresponding bit in the vector is set, then the element ***might*** be in the set. Otherwise, it's ***definitely not***. That's it. This intimidatingly named structure is that simple. In constant time, with extremely limited space usage (```m``` bits), we can answer the question of set membership with no false negatives. The values of ```m``` and ```k``` can be fine-tuned to minimize, within practical constraints, the probability of a false positive.
