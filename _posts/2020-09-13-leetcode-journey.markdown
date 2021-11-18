---
layout:     post
title:      "Solutions to LeetCode in Java"
subtitle:   "Plus advice for job interviews"
date:       2020-09-13 12:00:00
author:     "heapify"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - Red-Black Trees
    - Google Code Jam
    - ACM
    - Binary Heaps
    - Hash Mapping
---

**Timeline**
* `2020-09-11`: created [LeetCodeJourney](https://github.com/gengyu89/LeetCodeJourney)
* `2020-09-12`: composed Markdown files
* `2020-09-13`: hosted on GitHub and rendered as Jekyll pages

---

**Preface**

In geological sciences or other academic fields related to scientific computation, a few people believe that (1) programming is less difficult compared to mathematics, physics, and the interpretation of results, and (2) the programming tasks they are doing are close to computer science (CS) or software engineering (SE), which is why I am bothering explaining the basics.

As a person who has been studying in both Computer Science (2010-2012) and Geophysics (2015 to now), I personally cannot agree with those people who confuse *data processing* and *software making*. Most techniques used in geophysical studies have nothing to do with software development. They can be termed *coding* rather than *programming*. As developers, we always focus more on the readability and performance of our codes, the portability of the final product, and its capability of handling exceptions. Therefore, when people mentioned above use *similar* to describe the relationship, they probably confuse the theories.

> A real programmer does not believe these statements. They should have done a lot more research before drawing their conclusions!

**Why are data structures and algorithms so important?**

Software development, in a full lifecyle, relies on a variety of comprehensive skills. *Design patterns for solving algorithmic problems* - like many university programs taught us - are ideal abstractions of real-world problems. Merging software modules by multiple developers, compiling a legacy code and fixing incompatibility issues, or for some reason remaking an open-source library from scratch, etc. will definitely bring more pain than designing and implementing algorithms.

![devops](/img/in-post/post-leetcode-journey/Devops.svg.png)

Nevertheless, efficiency is still one of the most difficult aspects of programming, for that reducing the execution time of a code usually requires far more knowledge than making it work. From beginning to end, challenging algorithmic questions are the main focus of most technical job interviews. It has been widely addressed that algorithm engineers are paid higher salaries than other software developers, although some do not agree with this assertion. In any case, *data structures and algorithms* are primary skills for job hunters in IT to go to their dream places (big-name companies like Nvidia, for example).

From the micro-level, concepts like *Graph Theory* and *Discrete Mathematics* used by computer scientists describe models for data storage and nodal relationship, which usually results in an impact on execution efficiencies. Although this may not matter for small problems, the effect accumulates and eventually becomes significant as a project grows. Concerns about these aspects make CS/SE significantly different from other subjects that require code writing skills.

![rb_tree](/img/in-post/post-leetcode-journey/Red-black_tree_example.png)

Thanks to the innovative application of Red-Black Trees (time complexity: O[log N]) in the Linux kernel, searching in a dataset containing 1,000,000,000 elements requires 30 comparisons on average. In contrast, the most naive approach that traverses through an array linearly (time complexity: O[N]) requires many more operations than this. Since 2012 while studying in Canada, I have been fascinated by the beauty of data structures and the most well-known coding contest [Google Code Jam](https://codingcompetitions.withgoogle.com/codejam). In the computer industry, many people like me believe that skills in writing efficient codes make a gigantic difference (not only for ACM, particularly), for which I have been working much harder on learning algorithms rather than the syntactic aspect of programming languages.

**Commonly-used data structures and algorithms**

[LeetCode](https://leetcode.com/) is one of the most fabulous websites for testing your problem-solving abilities in "CS manners" (concretely, algorithm implementations based on proper usage of data structures). The time limitations of LeetCode's problems are super strict. Brute-force can be used to pass a limited number of them. To make things more challenging, all LeetCode problems have limitations on memory usage.

To help tackle these problems, I would like to provide some learning and preparation suggestions. They may work for both coding contests and job interviews.

There are two ways of categorizing data structures. My preferred method is to organize them with their linearities:

![data_structures](/img/in-post/post-leetcode-journey/DS_Classification.jpg)

From the other aspect, it is much more difficult to provide a list of "essential algorithms". There is no silver bullet for algorithm design - no universal technique that can solve every computational problem you'll encounter. The more you know, the better.

With that being said, there are several general design paradigms that can help you solve problems from many different application domains - the ones that are considered classical and the ones you must be familiar with even not in a coding contest situation. Some of those are listed here:

Algorithms on graphs
* Bellman-Ford (single source, weighted graphs) - O[\|V\|\|E\|]
* Topological Sort (directed acyclic graphs): Khan's algorithm - O[V+E]
* Floyd–Warshall (multi-sources, weighted graphs) - O[V<sup>3</sup>]
* Dijkstra (single source, weighted graphs):
  - O[V+Elog(V)] in a priority-queue
  - O[E+Vlog(V)] in a Fibonacci heap
* Minimum Spanning Trees: Kruskal - O[E log(V)], Prim - O[E+Vlog(V)]
* Strongly-Connected Components (directed graphs): Kosaraju - O[V+E], O[V<sup>2</sup>] with an adjacency matrix
* Connected Components (undirected graphs): Kosaraju - O[V+E], with post-order property preserved

Algorithms on strings
* Longest Palindromic Substring / Longest Symmetric Factor: Manacher - O[N]
* Dynamic Programming: KMP (failure function), shortest editing distance - check online for individual time complexity

Other algorithms
* Matrix Multiplication: Strassen's algorithm - depends on the recurrence, T(n)
* Dynamic Programming: knapsack (pseudo-polynomial), minimum coins of changes - check online for individual time complexity

<!-- See reference here: https://blog.asarkar.com/algorithms-design-analysis/final/ -->

Based on the preceding choices, optimization is quite necessary for sparse graphs, in which V is the number of vertices, and E is the number of edges. Also, note that the runtime may differ for specific problems.

![khans_algorithm](/img/in-post/post-leetcode-journey/Topological_Sort.png)

**Recommendation for Textbooks**

Here is a list of textbooks I had been using in my undergraduate CS Minor program. Some of them are ancient (published in 1980 or so) but are still useful.

Books that are programming languages specified:
* Structure and Interpretation of Computer Programs (SICP) - in Lisp
* Data Structures and Algorithms in Python by Michael T. Goodrich
* Data Structures & Algorithm Analysis in C++ by Mark A. Weiss
* Algorithms in a Nutshell: A Practical Guide - in Java and Python
* Algorithms, 4th Edition - in Java

Books that use pseudo codes or natural languages:
* Introduction to Algorithms, Third Edition \| The MIT Press
* Data Structures and Algorithms by Alfred V. Aho
* Algorithms Unlocked \| The MIT Press

For people who would like to improve their coding style in Python, here are books that I believe you have been looking for:
* Python Cookbook 3rd Edition
* Data Structures and Algorithms Using Python by Rance D. Necaise

<ins>Python Cookbook</ins> is the best reference to demonstrate *how to avoid programming like a novice*: closures, descriptors, containers, decorators, context managers, coroutines, lazy properties, attrgetter, itemgetter, and so on.

Python has many advanced features that can make object-oriented programming much more comfortable, manageable, and straightforward. Most people do not even know them, sadly. These features can help you improve your developing efficiency dramatically, although they are not in any way associated with execution efficiency.

<ins>Necaise</ins> is the best introductory-level guidance for abstract data types (ADT). Topics like the following can be found in this book:
* What is the definition of *average probing length*? Note: Donald Knuth gives this.
* When do primary and secondary clusterings happen in a hash mapping operation?
* List several ways of computing amortized cost (answer: aggregate method, bankers' method, physicists' method).
* How to merge two binary heaps in linear time? - my favorite question!

The preceding are fundamental questions in IT companies' job interviews.

Of course, a real job interview will be much more stringent than these.

Good luck with your applications!
