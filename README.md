# Introduction to NumPy's einsum
The einsum function is one of NumPy’s jewels. It can often outperform familiar array functions in terms of speed and memory efficiency, thanks to its expressive power and smart loops. On the downside, it can take a little while understand the notation and sometimes a few attempts to apply it correctly to a tricky problem.

There are quite a few questions on sites like Stack Overflow which about what einsum does and how it works, so this post hopes to serve as a basic introduction to the function and what you need to know to begin using it.

What einsum does
Using the einsum function, we can specify operations on NumPy arrays using the Einstein summation convention.

Suppose we have two arrays, A and B. Now suppose that we want to:

multiply A with B in a particular way to create new array of products, and then maybe
sum this new array along particular axes, and/or
transpose the axes of the array in a particular order.
Then there’s a good chance einsum will help us do this much faster and more memory-efficiently that combinations of the NumPy functions multiply, sum and transpose would allow.

As a small example of the function’s power, here are two arrays that we want to multiply element-wise and then sum along axis 1 (the rows of the array):

A = np.array([0, 1, 2])

B = np.array([[ 0,  1,  2,  3],
              [ 4,  5,  6,  7],
              [ 8,  9, 10, 11]])
              
How do we normally do this in NumPy? The first thing to notice is that we need to reshape A so that we can broadcast it with B (specifically A needs to be column vector). Then we can multiply 0 with the first row of B, multiply 1 with the second row, and 2 with the third row. This will give us a new array and the three rows can then be summed.

Putting this together, we have:

>>> (A[:, np.newaxis] * B).sum(axis=1)
array([ 0, 22, 76])
This works fine, but using einsum we can do better:

>>> np.einsum('i,ij->i', A, B)
array([ 0, 22, 76])
Why better? In short because we didn’t need to reshape A at all and, most importantly, the multiplication didn’t create a temporary array like A[:, np.newaxis] * B did. Instead, einsum simply summed the products along the rows as it went. Even for this tiny example, I timed einsum to be about three times faster.


