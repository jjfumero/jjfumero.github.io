---
title: 'Book Review: JVM Performance Engineering'
date: 2024-06-21
permalink: /posts/2024/06/21/book-review-jvm-performance-engineering
author_profile: false
tags:
  - Book
  - Review
  - JVM
  - Performance 
  - Performance Engineering
excerpt: ""
---

<img align="right" style="width:140px;" src="https://raw.githubusercontent.com/jjfumero/jjfumero.github.io/master/files/blog/24-06-book-review/JVM-Perf-Eng-Front.png">

**Disclaimer:** Pearson Editorial kindly asked me to review Monica Beckwith’s new book: [JVM Performance Engineering: Inside OpenJDK and the HotSpot Java Virtual Machine](https://www.amazon.co.uk/JVM-Performance-Engineering-OpenJDK-Developers-ebook/dp/B08F5J6V4T). Besides, I had the pleasure to have an early access to one of the chapters in this book to provide some comments and feedback related to the TornadoVM project as well as providing a critical view of the future trends of modern hardware support, like GPUs. Thus, for full disclosure, Pearson sent me a copy of this book for the sake of the review. However, this review is based on my honest opinion, and Pearson has not read this review before publishing.

## Thorough Performance Exploration of Java's Evolution

Monica Beckwith’s JVM Performance Engineering is a comprehensive and insightful exploration into the world of Java performance. I highly appreciate that Monica Beckwith, a Java Champion and a recognized expert in the field, has put significant effort into not only explaining the evolution of Java and the core components that affect performance, but also reasoning and analysing the performance improvements that have occurred over the years in OpenJDK.

One of the standout features of this book is its ability to offer a comprehensive overview of Java’s evolution and its impact on performance. For those of us who closely follow JVM development and innovations in OpenJDK projects, achieving a holistic understanding can be challenging. From my perspective, this book facilitates that global view, deepening my appreciation for the JVM. As a GPU/Compiler researcher myself, I have always been impressed by the engineering excellence of the JVM, and this book just reinforces it.

With the intention to understand how to perform performance analysis with emphasis in deep understanding of each of the components, this book also gives an overview of the past, present and future directions for each of the components that affected, affects and will affect performance. To name a few, Monica covers topics such as the Java type system, Java Modules, String handling improvements, different Garbage Collectors, JIT and Tier Compilation, Virtual Threads, to new projects such as Valhalla, GraalVM, Leyden and Babylon, and even Aparapi and TornadoVM!  Each of these topics has a direct or indirect connection to performance, and this is the key to this book. 

## Balanced Approach Theory and Practice 

> “Performance Engineering is about making informed decisions”. 

*Monica Beckwith.*

This quote could represent very well the flow of the book. It is all about making informed decisions, and to make informed decisions, we need a deep understanding of our applications’ logic and behaviour using tools, as well as understanding the environment the applications run on. As performance engineers, and more specifically, JVM performance engineers, we should understand all components of the software stack at a deep level. This is not just about software, but the intersection between application code, system software (e.g., the actual JVM), and hardware. The book reviews key components in all the software and hardware stack as well as gives practical examples on how to assess performance of our applications, which tools could be used and how to interpret the data for different case scenarios. 

There is also a good balance between theory and practice. I think many of us will agree with Monica in knowing the technology and theory behind all these topics will tremendously help to understand performance bottlenecks and how to approach performance. However, without real examples, some concepts might be hard to understand. As a reader, I appreciate that there are not only toy examples to illustrate the purpose of the JVM components explained, but also real examples from Monica’s experience that facilitate understanding and smooths the learning curve to these topics.

## Personal Learning Experience


On a personal note, despite my 10+ years of experience working with Java and the JVM, I found myself learning a lot from this book, especially for the topics that I do not deal with that much in the day-to-day job. Besides, it makes me pay more attention when I write code.

> “[Performance Engineering] enables us gather understanding of the enhancements that can yield runtime efficiency gains”
Monica Beckwith.

<img align="right" style="width:300px;" src="https://github.com/jjfumero/jjfumero.github.io/blob/master/files/blog/24-06-book-review/image.JPG?raw=true">

One of my favourite chapters (well, I guess apart from Modern Hardware in which Monica expands and talks about GPUs! yes, I am biassed) is chapter 5. This chapter sets the foundation and methodology to assess performance engineering from a more analytical approach. It shows not only tools (like JMH), but also key metrics to consider, and different ways to approach the performance assessment of our applications (e.g., top-down approach vs bottom-up approach).

And, when talking about performance, it is essential to understand the hardware in which applications run on. And chapter 5 also covers some hardware details. It gives an overview of the most important hardware components when running multi-threaded applications, such as hyperthreading and NUMA accesses, and their impact on performance, especially for the GC component and multi-thread applications. 

## Conclusion

JVM Performance Engineering by Monica Beckwith is an exceptional resource for anyone involved in Java development and performance engineering that will help to obtain more informed decisions about their applications and their impact on performance.

*Finally, do I recommend this book?* Well, you probably guessed it at this point. So yes, I highly recommend it. But, in my opinion, this book is not for beginners. You will take most of it if you are an experienced developer, and/or software architect who wants to go deep into the JVM, and understand how different components could impact performance. 


## References

- [JVM Performance Engineering: Inside OpenJDK and the HotSpot Java Virtual Machine](https://www.amazon.co.uk/JVM-Performance-Engineering-OpenJDK-Developers-ebook/dp/B08F5J6V4T)
