---
layout:     post
title:      "Solutions to LeetCode in Java"
subtitle:   "A summary about people's stupidness in Geophysics Major"
date:       2020-09-13 12:00:00
author:     "__restrict"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - 红黑树
    - Google Code Jam
    - ACM
    - 二叉堆
    - 哈希映射
---

**Preface**

In geological sciences or other academic fields related to scientific computation, many people believe that the programming they learned is close or similar to what people are doing in Computer Science (CS), which is why I am bothering myself explaining the basics. The truth is, most programming tasks in geophysics is about invoking finite element libraries through Application Programming Interface (API) without worrying about lower-level details, which is the main reason people think programming is (much) the derivation of mathematical equations and less important than the interpretation of results.

Annoyingly, after so many years studying in geophysics, I found the programming tasks in this major boring, or I can even use the word "lame" to describe them. In my opinion, the standard about programming is too low, and when people state "similar", they probably did not understand the above fact.

Our first discussion question must be, *Why do I consider geophysicists incapable of developing software?*, or *Why are programming more complicated than what most people think?*.

**Why are data structures and algorithms so important?**

Software development, in real cases, relies on a variety of comprehensive skills. "Design patterns for solving algorithmic problems" - like many university programs taught us - are ideal abstractions of real-world problems. Merging software modules by multiple developers, compiling and fixing a package in a modern environment, or for some reason remaking something from scratch, etc. will bring much more pain than designing and implementing algorithms.

Nevertheless, efficiency is still one of the most difficult aspects of programming, for that reducing the execution time of a section of code requires far more knowledge than making it work merely. Most IT companies' job interviewers evaluate their interviewees' abilities from the way they answer algorithmic problems. It has been widely addressed that algorithm engineers are paid higher salaries than other software developers, although some do not agree. In any case, "data structures and algorithms" are primary skills for programmers to go to their dream places (prestigious companies like Nvidia, for example).

![rb_tree](/img/in-post/post-leetcode-journey/Red-black_tree_example.png)

From the micro-level, concepts like graph theory and discrete mathematics used by computer scientists determine how objects/events can be allocated in computer memories and how each record should be accessed from its neighbors, which usually results in an impact on execution efficiencies. Although this may not matter for small problems, the effect accumulates and eventually becomes significant as a project grows. Concerns about these aspects make CS to some extent - although not totally - different from other subjects that require code writing skills.

Thanks to the innovative application of Red-Black Trees (time complexity: O[log N]) in the Linux kernel, searching in a dataset containing 1,000,000,000 elements requires 30 comparisons only. In contrast, a naive approach (time complexity: O[N]) usually requires many more operations than this. Since 2012 while studying in Canada, I have been fascinated by many coding contests, including the most well-known Google Code Jam. In the computer industry, many people like me believe that skills in writing efficient codes make a gigantic difference (not only for ACM, mainly), for which I have been working much harder on learning algorithms rather than the syntactic aspect of programming languages.

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

Based on the preceding choices, optimization seems quite necessary for sparse graphs, in which V is the number of vertices, and E is the number of edges. Also, note that the runtime may differ for specific problems.

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
