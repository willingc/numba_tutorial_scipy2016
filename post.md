# Numba: Tell those C++ bullies to get lost

This is the title of our [SciPy 2016](http://scipy2016.scipy.org/) tutorial, where we take aim at those who claim Python is not for science because its performance stinks. 
_Wake up, world!_ 
You've been able to make Python codes fast for a long time, with Cython, Swig, and good NumPy idioms. 
But there's a new kid on the block that blows it all out of the water: **Numba**.

Our SciPy 2016 tutorial description promised this: 
_"Come and learn how to accelerate your existing Python code by an order (or orders!) of magnitude using Numba. Join us for this tutorial as we take an existing NumPy-based Barnes-Hut Treecode and rewrite it using Numba. Barnes-Hut is an approximation algorithm for reducing n-body problems from O(n^2) to O(n log n). Don't know anything about treecodes? That's ok! You'll learn that, too!"_

In this blog post, we explain why we had to change how we deliver on our promise: you will *still* learn how to accelerate your Python code using Numba, but we won't be using a Barnes-Hut treecode as the application example.

Instead of a treecode, we'll use a series of separate example applications. 
We decided it will make for a better and more useful tutorial experience for all. 
Our plan for the tutorial is that participants *learn by doing*, using Numba to accelerate an existing code. 
The original idea was to speed-up a Barnes-Hut treecode step-by-step. 
But a few technical hurdles led to scrapping this idea, which proved to be inadequate for teaching Numba, given its current feature set.  

Learning about treecodes is great (we like treecodes!), but the goal is to introduce Numba and cover a broad, if not fully comprehensive, range of features. 
As I worked on the treecode example, I found that I had to shoehorn Numba features into odd places in the code. 
It felt like I was introducing these features in ways that I would not otherwise recommend. 
Worse, in my efforts to squeeze things in, I ended up rewriting more and more of the original code. 
That's not how the Numba experience should be.  

Accelerating your Python code with Numba starts with profiling the code to find bottlenecks. 
The Numba way is to then apply minimal code changes to performance-critical sections and gain substantial speed-ups. 
You don't want to refactor your entire package. 
The treecode application example was leading us to teach Numba the "wrong way" and we were troubled by this.  

The final nail in the coffin of the treecode example was that (as of version 0.26) Numba had no support for recursion, which is the simplest way to contruct and traverse tree-like data structures. 
Recursion is especially tricky because Numba uses type-inference to compile Python code using LLVM. 
Should it repeat the type-inference and compilation steps at each call of a recursive function?
That would tank performance.  
 
Lack of recursion support left us with three options. 
The first option: use Numba to accelerate the bottlenecks and keep the recursive functions in pure Python. 
This worked, but it was slow. 
So slow, in fact, that the naive Numba-accelerated direct-summation algorithm outperformed the more algorithmically efficient Numba-accelerated treecode. 
The Barnes-Hut algorithm scales as O(n log n) and direct summation as O(n^2 ): 
the treecode will outperform direct summation with a large-enough problem. 
In our tests, a "large-enough problem" was too big to fit in memory on most laptops, which does not make for a good tutorial experience.  

The second option: use an iterative method to construct and traverse the tree.
Any recursive algorithm has a non-recursive counterpart and Numba will support tree traversal this way. 
I started to implement that and then imagined myself saying this during the tutorial: 
_"Numba is a powerful and convenient tool to help accelerate your existing scientific codes. 
And it's easy to use, too!  We'll begin by manually programming our own octree stack with push and pop methods..."_ Ugh.

We went with the third option: change the tutorial application. 
We think you'll like it!  We'll work on an iterative pressure Poisson solver and even
delve into some (beginner) multigrid stuff. 
Yes, there will be a smattering of semi-contrived examples but we think they'll provide a solid foundation and reference for applying these lessons to your own projects.  

If you can't join us on July 11th at Austin, catch the tutorial later on YouTube!

A final addendum:  
We shared some of this saga with Travis Oliphant at PyCon2016 and thanks to the unceasing efforts of Stan Seibert and the Numba team, support for direct recursion will be available in Numba 0.27, slated for release the week of July 7th.  Just in time for SciPy!  But sadly, not for the treecode tutorial.  Maybe we'll see you next year?

https://twitter.com/teoliphant/status/744972577056002048
