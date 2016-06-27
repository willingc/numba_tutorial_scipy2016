We are changing the application example for our Numba tutorial at SciPy 2016. Here’s why.

Barba group member Gil Forsyth and Professor Barba are presenting the tutorial "Numba: Tell those C++ bullies to get lost" on July 11th, at SciPy2016 in Austin, TX.  

We're shifting towards a series of separate example applications. 
We believe it will make for a better and more useful tutorial experience for all.  
The original plan was that attendees would learn by doing, using Numba to accelerate an existing Barnes-Hut treecode step-by-step.  
There were a few technical hurdles which led to scrapping this idea (more on this later), but it also proved to be a less than ideal teaching tool.  

Foremost, this is a tutorial on how to use Numba.  
Learning about treecodes is great (we like treecodes!), but the goal is to introduce Numba and cover a broad, if not fully comprehensive, swath of available features.  
In constructing the first draft, I found myself attempting to shoehorn Numba features into odd places in the code.
This was in the service of introducing these features, but introduced them in ways that I would not otherwise recommend.  
Furthermore, in my attempts to squeeze things in, I ended up rewriting more and more of the original code.  
This runs counter to the proper use of Numba.  

The typical way you would use Numba is to first profile your code to find bottlenecks, then accelerate the problematic portions with minical code changes.  
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
