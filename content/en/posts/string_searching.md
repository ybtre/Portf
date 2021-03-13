---
author: "Ritschie"
title: "String Searching - Data Structures and Algorithms"
date: 2020-12-17T12:00:06+09:00
description: "Comparing Boyer-Moore vs Rabin Karp"
draft: false
hideToc: false
enableToc: true
enableTocContent: false
author: Hristo Vuchev
authorEmoji: 👻
image: /PostImages/str_search.png
tags: 
- algorithms
- cpp
categories:
- university
- coursework
series:
- University Projects
---
Year 2 we started learning about different kinds of data structures and algorithms as well as applying them.

For the assessment project we had to create an application which implements two different standard algorithms with the appropriate data structures and the ability to vary the size of the input data.

I chose two different string searching algorithms. The Rabin Karp algorithm which is utilizes a brute force process and the Boyer Moore algorithm, which utilizes a skipping approach to string searching.

The performance of the algorithms was compared using the following inputs in an abstract from the book "Lord of the Rings":
- most common english words
- most common english letter
- least common english letter
- string that is not contained in the text
- string at the end of the document
- very long string that is contained in the text
</br>

Testing with both algoritms I came to the conclusion that the Boyer Moore algorithm excels when it has to skip more in between found strings. When both algorithms are searching for a common string inside the text, the performance difference is close between both algorithms. If you are searching for something more “rare” or less likely to be contained in the text, then use Boyer Moore.