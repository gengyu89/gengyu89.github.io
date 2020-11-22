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
* `2020-08-10`: created [LeetCodeJourney](https://github.com/gengyu89/LeetCodeJourney)
* `2020-09-12`: composed Markdown files
* `2020-09-13`: hosted on GitHub and rendered as Jekyll pages

---

**Preface**

In geological sciences or other academic fields related to scientific computation, many people believe that the programming they learned is somehow close to software engineering (SE) or computer science (CS), which is why I am bothering myself explaining the basics. In my opinion, it is very naive to say these things. Programming can be either (1) analyzing data or (2) making software. When people run FDTOMO, ASPECT, TOMOG3D, BurnMan, FEniCS, etc. to solve geophysical problems, what they are doing is mainly (1).

Unfortunately, data analysis does not touch the core concepts of computer science quite much, which makes geophysicists incapable of becoming developers without having a firm background in CS. Algorithms, data structures, UI design, and API development... these skills are much more important as a software engineer. After so many years of studying in both computer science and geophysics, I can see these two subjects have completely different focuses, and when people state "similar", they probably confuse the theories.

> A real programmer does not believe it. They should have done a lot more research before drawing this conclusion!

**Why are data structures and algorithms so important?**

Software development, in real cases, relies on a variety of comprehensive skills. "Design patterns for solving algorithmic problems" - like many university programs taught us - are ideal abstractions of real-world problems. Merging software modules by multiple developers, compiling a legacy code and fixing incompatibility issues, or for some reason remaking something from scratch, etc. will definitely bring more pain than designing and implementing algorithms.

Nevertheless, efficiency is still one of the most difficult aspects of programming, for that reducing the execution time of a section of code requires far more knowledge than making it work. Most IT companies' job interviewers evaluate their interviewees' abilities from the way they answer algorithmic problems. It has been widely addressed that algorithm engineers are paid higher salaries than other software developers, although some do not agree. In any case, "data structures and algorithms" are primary skills for programmers to go to their dream places (prestigious companies like Nvidia, for example).

![rb_tree](/img/in-post/post-leetcode-journey/Red-black_tree_example.png)

From the micro-level, concepts like graph theory and discrete mathematics<sup>[1]</sup> used by computer scientists determine
how items/objects can be allocated in computer memories and
how each record can be accessed from its neighbors, which usually results in an impact on execution efficiencies. Although this may not matter for small problems, the effect accumulates and eventually becomes significant as a project grows. Concerns about these aspects make CS/SE significantly different from other subjects that require code writing skills.

[1] Compared to CS, the geophysical way of programming is much more intuitive. In most cases, you only need to transform some equations into source codes.

Thanks to the innovative application of Red-Black Trees (time complexity: O[log N]) in the Linux kernel, searching in a dataset containing 1,000,000,000 elements requires 30 comparisons only. In contrast, a naive approach (time complexity: O[N]) usually requires many more operations than this. Since 2012 while studying in Canada, I have been fascinated by many coding contests, including the most well-known [Google Code Jam](https://codingcompetitions.withgoogle.com/codejam). In the computer industry, many people like me believe that skills in writing efficient codes make a gigantic difference (not only for ACM, mainly), for which I have been working much harder on learning algorithms rather than the syntactic aspect of programming languages.

**Commonly-used data structures and algorithms**

[LeetCode](https://leetcode.com/) is one of the most fabulous websites for testing your problem-solving abilities in "CS manners" (concretely, choosing proper data structures, and implementing algorithms to satisfy specific requirements). The time limitations of LeetCode's problems are super strict. Brute-force can be used to pass a limited number of them. To make things more challenging, all LeetCode problems have limitations on memory usage.

To help tackle these problems, I would like to provide some learning and preparation suggestions. They may work for both coding contests and job interviews.

There are two ways of categorizing data structures. My preferred method is to organize them with their linearities:

![data_structures](/img/in-post/post-leetcode-journey/DS_Classification.jpg)

From the other aspect, it is much more difficult to provide a list of "essential algorithms". There are numerous algorithms in the world of computers, and the number is growing continually (the more you know, the better).

That being said, exquisite algorithms you come up with by afflatus must be built upon fundamental ones - the ones that are considered classics and the ones you must be familiar with even not in a coding contest situation. Some of those are listed here:
* Bellman-Ford (single source, weighted graph) - O[\|V\|\|E\|]
* Topological Sort (directed acyclic graphs): Khan's algorithm - O[V+E]
* Floyd–Warshall (multi-sources, weighted graphs) - O[V^3]
* Dijkstra (single source, weighted graphs):
  - O[V+Elog(V)] in a priority-queue
  - O[E+Vlog(V)] in a Fibonacci heap
* Minimum Spanning Tree: Kruskal - O[E log(V)], Prim - O[E+V log(V)]
* Dynamics Programming: knapsack (pseudo-polynomial), KMP (failure function), shortest editing distance, minimum coins of changes - check online for individual time complexity

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

<ins>Python Cookbook</ins> is the best reference to demonstrate "how to avoid programming like a novice": closures, descriptors, containers, decorators, context managers, coroutines, lazy properties, attrgetter, itemgetter, and so on.

Python has many advanced features that can make object-oriented programming much more comfortable, manageable, and straightforward. Most people do not even know them, sadly. These features can help you improve your developing efficiency dramatically, although they are not in any way associated with execution efficiency.

<ins>Necaise</ins> is the best introductory-level guidance for abstract data types (ADT). Topics like the following can be found in this book:
* What is the definition of "average probing length"? Note: Donald Knuth gives this.
* When do primary and secondary clusterings happen in a hash mapping operation?
* List several ways of computing amortized cost (answer: aggregate method, bankers' method, physicists' method).
* How to merge two binary heaps in linear time? - my favorite question!

The preceding are fundamental questions in IT companies' job interviews.

Of course, a real job interview will be much more stringent than these.

Good luck with your applications!
