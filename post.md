# Numba: Tell those C++ bullies to get lost

This is the title of our [SciPy 2016](http://scipy2016.scipy.org/) tutorial, where we take aim at those who claim Python is not for science because its performance stinks. 
_Wake up, world!_ 
You've been able to make Python codes fast for a long time, with CPython, Swig, and good NumPy idioms. 
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

To speed up your Python code with Numba, you first profile the code to find bottlenecks, then accelerate the problematic portions with minimal code changes.  
You don't want to refactor your entire package and neither does anyone else.  
This is the use case that tools like SWIG and Cython help meet.  
Again, we don't want to teach Numba the "wrong way" in the service of breadth of instruction.  

The final nail in the coffin of the treecode tutorial was recursion.  
Specifically, the lack of support for recursion in Numba (as of version 0.26).  
Numba uses type-inference to compile Python code using LLVM and recursion presents a tricky problem in this regard.  
Will you repeat the type-inference and compilation step at each call of a recursive function?  
That will tank any possible performance gains, and likely provide slower code overall.  

Recursion is the simplest way to contruct and traverse tree-like data structures.  
This missing feature left us with three options.  

The first option: use Numba to accelerate the bottlenecks and keep the recursive functions in pure Python.  
This worked, but it was slow.  
So slow, in fact, that the naive Numba-accelerated direct-summation algorithm outperformed the more algorithmically efficient Numba-accelerated treecode.  
Barnes-Hut treecode is O(n log n).  Direct summation is O(n^2).  With a large enough problem, the treecode will eventually win out.  
It turns out a "large enough problem" is too big to fit in memory on most laptops which does not make for a good tutorial experience.  

The second option: use an iterative method to construct and traverse the tree.
Any recursive algorithm has a non-recursive counterpart and Numba will support tree traversal this way.  
I started to implement this and then imagined myself saying this during the tutorial.  
"Numba is a powerful and convenient tool to help accelerate your existing scientific codes.  
And it's easy to use, too!  We'll begin by manually programming our own octree stack with push and pop methods..."

We went with the third option: change the tutorial application.  
We think you'll like it!  We're going to spend time working on an iterative pressure Poisson solver and even
delve into some (beginner) multigrid stuff.  And yes, there will be a smattering of "contrived" examples but 
we think they'll provide a solid foundation and reference for applying these lessons to your own projects.  

We hope you can still join us on July 11th!  If not, you can always catch it on YouTube later!

One final addendum:
We shared some of this saga with Travis Oliphant at PyCon2016 and through the unceasing efforts of Stan Seibert and
the Numba team, support for direct recursion will be available in Numba 0.27, slated for release the week of July 7th.  
Just in time for SciPy!  But sadly, not for the treecode tutorial.  Maybe we'll see you next year?
