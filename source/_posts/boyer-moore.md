---
title: Understanding Boyer-Moore's Voting Algorithm
---

Boyer-Moore voting algorithm is used to find the majority element in an array. By 'majority',
it refers to an element appearing for more than half times of the total elements. 
The problem itself is very easy to solve though——you can just iterate the array,
count their occurrences, and check if there is one more than majority. 
It takes O(n) time and O(n) space, which is not bad. 
The Boyer-Moore algorithm, on the other hand, can optimize the space complexity to O(1).
Let’s see how it achieves that. It has two phases:

### Phase 1
```
# Suppose we have an array a
candidate=None
cnt=0
for x in a:
    if cnt==0:
        candidate=x
        cnt=1
    elif x==candidate:
        cnt+=1
    else:
        cnt-=1
```
### Phase 2
```
# Check if number of occurrences of candidate>len(a)/2
# The code is simple, just skip
```
This algorithm looks easy but takes some thought to understand. 
Its correctness can be strictly proved. Here I will provide an 
intuitive thought process: the `candidate` is the element so 
far that has the potential to become majority element 
and `cnt` is the occurrences of `candidate` minus 
that of non-candidate so far. If there is a majority element `m`, 
then we will eventually meet it and it will ‘beat’ all 
other candidates because there is at most one majority element 
and it must have the most occurrences.

In addition, Phase 2 is needed because when there is no majority
element in the array, we will still get a `candidate` at the end. 
We need another pass to check if that `candidate` is indeed the 
majority element of the array. 

One misunderstanding, which I firstly have, is that 
`cnt` equals number of majority minus number of non-majority
after Phase 1. This is not necessarily true, 
considering case [1,2,3,3,3] in which `cnt` is 3 instead of 1 after Phase 1.
Actually that relationship only holds after the last time 
majority element becomes 
`candidate`. So `cnt` could be different for different arrangements of the 
same array.


This algorithm optimizes the space complexity to O(1) but
that still doesn’t make it seem very useful.
Next I will show a use case that makes it shining.

Suppose we have a long array a and there is a stream of 
query `(l,r)` which asks for the majority element of subarray `a[l…r]`
(refer to LC1157). We need an efficient algorithm for handling
the query, better than O(n). It’s natural to think about
using segment tree, which is also my first thought. 
The key of segment tree is to figure out how to merge the 
information of two child nodes and I encountered the 
difficulty here: how can the majority element
of a range be derived from those of its two subranges?
It turns out Boyer-Moore's Voting Algorithm can help. 
Here is the merge algorithm of it:

```
# a,b are two segment tree nodes
# merge function returns the candidate and cnt for merged range of a,b
def merge(a,b):
    if a.canditate == b.candidate:
        return a.candidate, a.cnt+b.cnt
    elif a.cnt>b.cnt:
        return a.candidate, a.cnt-b.cnt
    else:
        return b.candidate, b.cnt-a.cnt
```
`if a.canditate == b.candidate` is easy to understand while the
other two cases are quite hard to understand,
at least for me. It actually says when the majority elements 
of two subarrays are 
different, we update the `cnt`
to be their difference and choose the larger one as the `candidate`
for the combined array. 

We can do it because it's like performing Boyer-Moore algorithm on the 
combined array `a, b`. Suppose `x = a.candidate`, 
`y = b.candidate`, and `a.cnt < b.cnt`, we can rearrange 
`a` and `b` so that `a` ends with 
`a.cnt` `x`'s and `b` starts with `b.cnt` `y`'s. This is possible 
because there are at least
`a.cnt` `x`'s in `a` and `(len(a)-a.cnt)/2` pairs of 
different numbers that can be cancelled out, similarly for `b`.
Here `len(a)-a.cnt` is also guaranteed to be even because you
can image `cnt` is everything left after one-to-one cancellation. See an
example below.

<img src="/images/moore.jpg" width="500">

After this rearrangement, `a.cnt` and `b.cnt` still hold (you can try to run Phase 1 on them separately).
When you run the algorithm on the combined array, you will see
the final candidate is `b` and `cnt` is `b.cnt - a.cnt`. That
explains the merge function used in the segment tree.








         
 
