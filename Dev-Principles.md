### Development Principles

### 1) The Zen of Python

Enter Python shell and type "import this". The following appears.

    The Zen of Python, by Tim Peters

    Beautiful is better than ugly.
    Explicit is better than implicit.
    Simple is better than complex.
    Complex is better than complicated.
    Flat is better than nested.
    Sparse is better than dense.
    Readability counts.
    Special cases aren't special enough to break the rules.
    Although practicality beats purity.
    Errors should never pass silently.
    Unless explicitly silenced.
    In the face of ambiguity, refuse the temptation to guess.
    There should be one-- and preferably only one --obvious way to do it.
    Although that way may not be obvious at first unless you're Dutch.
    Now is better than never.
    Although never is often better than *right* now.
    If the implementation is hard to explain, it's a bad idea.
    If the implementation is easy to explain, it may be a good idea.
    Namespaces are one honking great idea -- let's do more of those!

We respect these rules of Python, in particular: Explicit is better than implicit; Flat is better than nested.

See [Zen of Python](http://stackoverflow.com/questions/228181/zen-of-python) on StackOverflow. Specifically, [these examples](http://artifex.org/~hblanks/talks/2011/pep20_by_example.html) are excellent.

### 2) Take time to get the right interfaces

Writing code that works is easier than writing interfaces that stand the test of time… You must consider how people down the line will use and evolve the code you are writing and make your code expressive, easy to understand, easy to read.

### 3) Don't repeat yourself

If you find yourself copying code from one place to another, you are probably doing it wrong… Use a mix-in. Use a parent class. Use a util file with a bunch of common functions. Do whatever you need to do to not repeat code.

### 4) Optimize the fast path

In most parts of the code, we favor clarity over performance. However, there are key sections of code that an outsize portion of our site run on top of / depend on. When we identify one of these sections, we optimize at the expense of clarity and document heavily.

Identifying these key sections in advance is **tough**, which is why Donald Knuth wrote that ["premature optimization is the root of all evil"](http://c2.com/cgi/wiki?PrematureOptimization)).

### 5) Two pieces of code that do roughly the same thing should look the same.

When looking at two classes that calculate metrics using Pandas, two views that display a chart… We look for the code to be a similar as possible, unless there is a good reason for them to be different. Similar structure, similar naming, similar spacing. Sameness is valued over slight optimizations or creative flourishes.

### 6) Always Be Refactoring

[Technical debt](http://www.codinghorror.com/blog/2009/02/paying-down-your-technical-debt.html) does not happen overnight--it's the culmination of a thousand small slights let slide. To avoid this, we spend 10-20% of our time refactoring, renaming, restructuring, questioning existing code.