---
layout: post
title: Book Review on World of Abstract Mathematics
date: 2024-03-17 10:14:00-0400
description: an example of a blog post with table of contents on a sidebar
tags: abstract maths
categories: maths
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

This post shows how to add a table of contents as a sidebar.

## Adding a Table of Contents

To add a table of contents to a post as a sidebar, simply add

```yml
toc:
  sidebar: left
```

to the front matter of the post. The table of contents will be automatically generated from the headings in the post. If you wish to display the sidebar to the right, simply change `left` to `right`.

## My Background

My background in mathematics is very average, calculus in secondary school advanced calculus in my first and second year in uni, some statistics and that was it, but I was one of those kids that enjoyed the subject and wasn't afraid of it, I also didn't understand why people hated it. My best homework were maths problems. During my A levels, I solved all of the problems in my <a href="https://www.pinterest.com">coursework texbook</a>  for the fun of it! I would solve problems all the time, during my break time, while eating, while waiting for the next teacher to come to class, and when I didn't feel like studying other subjects. I also led my maths tutorial group that was designed to help other students struggling with maths. Basically, maths has never been my problem but that was all it was to me learn the formula and steps to solve the problem plug in the numbers, recognise the curveballs etc. As I started to move further in my career first, in software engineering and now in AI research I realise that I never really understood the subject, like why a theorem existed and how to apply them in solving real world problems. I could get away with it in SWE but now as an AI researcher I have realised that if I'm ever going to be able to make meaningful contributions to my field, then I have to fill up the gaps in my knowledge of the subject. This has led me on a journey to reading a series of mathematical textbooks the first of which is “World of Abstract Mathematics” which I'll be reviewing in this post.

## About the book

The book was written by Brendan Sullivan as a part of his thesis in the fulfilment of his PhD program at Carnegie Mellon University. It gives a good Introduction into fundamental theoretical concepts in mathematics such as set theory, logic, relations etc with a focus on mathematical proof techniques. It emphasises on the preciseness of the subject, and gives an insight into how mathematicians think about their work. In the next section, I'll briefly go over each chapter, and In future posts I will delve deeper into some of the concepts I found interesting. If you're aiming to thoroughly explore this book, this post serves as an excellent primer, since having a broad understanding of the subject matter you're about to study is always beneficial.

## Chapter Summaries

<!-- {:data-toc-text="Customizing"}

If you want to learn more about how to customize the table of contents of your sidebar, you can check the [bootstrap-toc](https://afeld.github.io/bootstrap-toc/) documentation. Notice that you can even customize the text of the heading that will be displayed on the sidebar. -->

### Chapter 1: What is mathematics?

I have always thought that there were no absolute truths, like how can we know something is true or even always true when there are much more unknowns magnitudes higher, than what we currently know. Also if we do something repeatedly 1000 times and it gives us the same result do we consider it to be always true? How do we know that the result would be the same the 1001th time we perform the experiment?

As Brendan explains in this chapter , this is one of the questions mathematics seeks to answer, and it answers this question by mathematical proofs. So if you've ever wondered about why mathematicians put themselves through what looks like a painful and really boring process of writing proofs here's your answer. My mind also goes back to the constant disagreement Ramanujan's supervisor had with him about his methods even though he was such a brilliant mathematician, this was the basis of the argument.

Let's look at  Goldbach's conjecture for example. It says that every even number greater than or equal to 4 can be written as the sum of two primes (yeah go ahead and try it for a couple of numbers) but there's no mathematical proof for this and we can't physically compute this for very large numbers hence there's no way for us to tell that the mathematical statement is true.

Devising new statements, finding if they are true or false (by making arguments) and sharing this with other people is what mathematics is about. The way I think of it Mathematics finds truths, physics use these truths to explain how the world works.

Dr/Prof  Brendan also did a good job of using various examples to show what a correct mathematical proof should constitute of, clarity and valid logical inferences at every step or claim in the proof.

If you look closely at many almost “creative” and funny proofs of how 1 + 1 is equal to 1 or how to prove Xmas mathematically, making it feel like mathematics acknowledges its divinity, they are all just pure manipulation or contain incorrect mathematical statement. Many times this proofs assume a fact and deduce something is true from it but and there is a clear valid logical inference at every step but assuming a fact and then deducing something is true from it does not mean the assumed fact is true! In chapter 5 we would see an example of how we properly make assumptions in a proof.

Mathematics identifying patterns associating them with definitions which we then need to proof

Mathematics contains symbols to make our definitions more concise

The rest of this chapter briefly brushed through some mathematical concepts and techniques Logic, Algebra, polynomials, Sets, and some puzzles to show how mathematics can be applied to solve problems. One of the puzzles here is the full monty hall problem and Dr Brendan does a good job of explaining it so well (It's hard to find resources that explains the solution to the problem very clearly imo).

Many mathematicians, likewise myself are of the opinion that there are many truths out there waiting to be discovered, the only difference is that they spend their time learning about the truths that have already been discovered in the hopes of exposing more truths

I found out in this chapter that Dr Brendan is also a fan of Top Gun Maverick which made me eager to listen to everything else he had to say in this book.

### Chapter 2 and 5: Mathematical Induction

As we established in c1 a lot of mathematics is finding truths via mathematical proofs, and a major focus of this book is mathematical proof techniques. There are proof techniques that have been repeatedly applied and generalised to argue certain kinds of mathematical problems or statements. One of the major proof techniques is mathematical induction which is basically making inductive arguments for problems with an inductive structure i.e dependence of one part of the result to a previous result. This topic was first introduced in c2 and then more rigorously discussed in c5 after some necessary discussions on set theory and logic.

In c2 we looked at inductive arguments where a fact depends on an immediate previous fact and arguments where a fact needs more information than a previous fact. I'll give an example of both cases.

Sum of first n odd integers

Can P1 always win

We also looked at explaining mathematical induction using 3 different analogies. I'll describe the domino analogy.

If you are a programmer you would already notice that mathematical induction is also the basis

of recursive programming.

C5 was a more formal introduction to the topic, mathematical induction as a principle was properly defined mathematically PMI which says ————-

Some questions that might pop up is what happens when the base case is not 1, what happens in a scenario where the inductive argument is backwards, these are the variants of induction. Another variant is inducting on even/odd numbers.

Guess what, all we've discussed so far is known as regular induction and there is such a thing as strong induction. Let me pull a Dr Brendan on you here, can you guess the difference?

Well with regular induction we say that p(k) is sufficient for us to deduce p(k+1), but with strong induction p(1), p(2)….p(k) are all sufficient to deduce p(k+1). Here's the principle of SPMI ————-

Finally we show an equivalence relationship between PMI and SPMI i.e whenever we need to prove something by PMI we can as well use SPMI, notice that the converse is also true. We'll see what an equivalent relationship means in c4 but for now if A is equivalent to B, A implies B also means that B implies A.

One thing I picked up here is also solving problems using geometric intuition e.g In the sum of n odd integers above we can solve it in a geometrical way like this ——


### Chapter 3: Set Theory

You've probably heard that set theory is the foundation of mathematics, Dr/Prof Brendan puts it this way “Everything we do in mathematics is built upon the foundation of sets” but based on what you did in high school and depending on which high school you went to you can probably only think of venn diagrams and you are wondering how Venn diagrams can be the foundation that the mathematics is built upon. It might help you to drop the idea of Venn diagrams for a moment as you begin to read this chapter and get ready to take on a new perspective.

Really what set theory does is that this it gives us a way to talk about mathematical objects and how to work with them. Let's rewind back a little. Mathematics is about truth finding, to find truths we identify patterns, make hypothesis in form of mathematical statements, then we proof that this statements are true or false using logic and mathematical techniques based on existing truths(that have also been rigorously proved). In mathematical statements we talk about mathematical objects. Try to recall any formal mathematical theorem you know maybe Pythagoras theorem, you'd often here things like set of Natural numbers, or set of positive Integers, these are the mathematical objects that sets theory allows us to talk about i.e with set theory we can group a collection of objects that have a well defined property. This collection of object is what is called a set and is denoted with {}. Basically we can group a collection of objects that have a common property and make a mathematical statement about it which we can then prove to be true or false. Another example that we've seen before is the statement “The sum of first n odd numbers is n^2” can you identify the mathematical object here? You're right if you say odd numbers. Of course odd numbers is a set that contains integers x that have the property that x divided by 2 is 1. Integers are mathematical objects too and odd numbers are subsets of Integers.

One thing to clarify here is the difference between “a subset of ” and “an element of”. Dr Brendan uses the analogy of a bin bag here, assuming you have two bin bags A and B if you take out all the elements of B and you can find them in A, then B “is a subset of” A, notice that B is not a bin bag inside of A. If it was inside of A then it would be “an element of” A.

Set operations that you are familiar with like “Intersection” and “Union” allows us to operate on multiple sets to produce a new one.

There are times we need to prove that a set A is equal to a set B and the way we prove that is to show that A is a subset of B and B is a subset of A. This is known as double containment and you would see it used in other chapters. And how do we prove that a set A is a subset of a set B, we simply show that any arbitrary element of A is also an element of B.

Disproving by counterexamples.

One very handy thing you'd learn in this chapter is writing in the set builder notation which helps to write clear and concise mathematical proofs

Dr Brendan mentions that interested individuals can dive deeper into set theory and I've heard the book [naive set theory](https://www.amazon.co.uk/Naive-Theory-Dover-Books-Mathematics/dp/0486814874/ref=sr_1_1?crid=KRPOHZYERMI0&dib=eyJ2IjoiMSJ9.3EgWTp0-O6PgGV1b6ZjpjtfHTNB7tYIV5lvj1zA9oNDkPZtmm_qrZLnfPQ6KcDyZeKHrEMiY0nQocJsnEDPUBwVnKFdw3Jh7VHc8dVRMPOGElLaCwuJBvO3-2p_roBGsJAmMzIJH1JyvEBSlztBsSFoXQJc4wj4uDP8cVVpmKmGHATNDtguzXAqB9qChgJIz0EozkK1tM4d2V8DhJlZ1oFrO2c4bG2t8GsGXF_wnhmk.8Tk8EYIyumSC5kQTFpO7PVWYtvYfJaH5ZA-N-hhIZp4&dib_tag=se&keywords=naive+set+theory&qid=1710081227&sprefix=naive+set+t%2Caps%2C91&sr=8-1) is a good place to start.


### Chapter 4: Logic

The previous chapter looked at one component of mathematical statements; mathematical objects, this chapter looks at another component which is logic. Both components helps us to write mathematical statements and proofs in a clear and precise manner.

Write in a language made up of these notations that we can share with other mathematicians such that they know exactly what we mean. Notations to help express logical ideas precisely and concisely. Assigning truth values to these statements.

Basically logic gives us 2 things;

1. Notations to express logical ideas in mathematical statements
2. Some for of rules to make logical inferences about mathematical statements. We already have an intuition on how to do this as humans as shown in our conversations with other humans.

Both of these things alongside the mathematical objects introduced in the previous chapter helps us to communicate our ideas in a clear and precise manner.

 But what exactly do we mean by mathematical statements/proposition, we've seen a few examples. A statement that is grammatically correct AND has just one truth value i.e it can either be true or false, not both or neither, not relatively or subjectively true(maybe what differentiates maths from philosophy?). The idea that a mathematical statement can either be true or False is actually one of the axioms of mathematics called “The Law of excluded middle“

What about statements like “x^2 - 1 = 0” you might ask. These are variable propositions cause their truth values depends on the value of the variable x, e.g this statement is True when the value of x is 1 but False when the value of x is 8. Variable propositions like this can be treated as a mathematical statement when we add some constraints to the values of that the variable can take.

Now that we are clear on what a mathematical statement is let's now see the role that logic plays in writing and proving them.

The first tool logic gives us are Quantifiers.

Quantifiers helps us to not just *shorten* mathematical statements but to also ***analyse them (as we would soon see).*** There are two quantifiers, a universal quantifier which connotes phrases like “for all”, “for every” and existential quantifier which connotes statements like “there exists”

Here are a few examples of how they are used in mathematical statements.

In terms of analysing mathematical statements, “quantification fixes a variable” like when we say for all n, there exists a and b. It's like having a nested loop where the outer loop fixes a value of n and the inner loop iterates through various values of a and b.

Also when we want to contradict a statement in a proof by contradiction we take a logical negation of the statement and assume that to be true. When we use this quantifiers we can easily negate them y turning every “for all” to “there exists”. This is an example of their usefulness other than to shorten statements

The next tool that logic gives us are the  logical connectives “and”, “or”, “implies”, their corresponding notation “^”, “|”, “⇒” this we are more familiar with. They help us make more complicated mathematical statements composed from simpler ones.

We are familiar with the fact if we have mathematical statements P, and Q

1. The mathematical statement P ^ Q holds true if P is True and if Q is true
2. The mathematical statement P|Q holds true when at least one of P and Q is True

A more confusing connective is P ⇒ Q  i.e “If P then Q” or “P implies Q” again Dr Brendan's analogy was super helpful in understanding this.

> “Let’s say that I make the claim If you work hard, then you will get an A in this course”. Here, P is “You work hard” and Q is “You will get an A”. When can you call me a liar? When can you declare I told the truth? Certainly, if you work hard and get an A, I told the truth. Also, if you work hard and don’t get an A, then I lied to you. However, if you don’t work hard, then no matter what happens, you don’t get to call me a liar! My claim didn’t cover your situation; I was assuming all along you would just work hard! Thus, I didn’t speak an untruth and so, by the Law of the Excluded Middle, I did speak the truth.”
>

 Some confusions to clear here

The truth value of the statement P ⇒ Q tells us nothing about the truth value of P or the truth value of Q

P ⇒ Q does not necessarily connote deduction of the conclusion from the hypothesis. In fact it is possible that P and Q are not connected in any way. Although you would often come across deductive expressions of this in mathematical proofs.

P ⇒ Q does not necessarily mean that the converse Q ⇒ P holds this would mean logical equivalence (which I mentioned briefly in the previous chapter).

If P and Q are logically equivalent i.e P < = > Q then P and Q have the same truth values regardless of what the truth values are. This is more commonly known as “P if and only P”

This is actually more of a biconditional statement because we are talking about two conditional statements at once. Logical equivalence and biconditionals both convey the same idea, the only difference is with logical equivalence you have no idea what P and Q is only that they have the same logical truth value.

When we have a biconditional statement “P if and only if Q” what we are really saying is “P if Q” i.e if Q holds then P holds and “P only if Q” i.e P can only hold if Q holds, or If P holds then Q definitely holds. The first is a sufficient condition (Knowing the property Q of a mathematical object is a sufficient condition to conclude the property P or we can guarantee P if we can guarantee Q) while the latter is a necessary condition (For P to even have a chance of holding then Q has to have held, The property Q is essential for P to hold).

The rest of this chapter shows some correlations between set operations and logic connectives. Then how to prove mathematical statements based on the presence of these logical notations and introduces other proof techniques like proof by contradcition.


### Chapter 6 & 7: Relations and Functions

Chapter 6 Introduces relations which help us talk about Functions in chapter 7 in a more mathematical way.

Relation is a set that relates the elements of two sets. Relations is a way of encoding information about the elements of two sets. It is a way of comparing elements of 2 sets and saying that they satisfy some property. Think of the function x^2 and its set {(2,4), (3, 9)} this is a way of comparing the set of Natural numbers and saying one number is a square of the other.

Spoiler: A function is a special kind of relation

Now irrespective of the function or relation defined on two sets, there are certain common properties that they tell us about their elements.  These properties are *Reflexive, symmetric, transitive, antisymmetric.* Whenever we are given a relation on a set we can check if this relation has all of these properties, we can even take a set and try to construct a relation that satisfies these properties.

An important type of relation that was extensively discussed in this chapter is the equivalence relation. An equivalence relation is a relation that has the following properties it is reflexive, transitive and symmetric. An equivalence relation partitions a set into equivalence classes. For example Let's look at the relation (x)R(y).

A partition is a way of breaking a large set into smaller sets in a way that they do not overlap

You can think of it as grouping related elements into a club and we can refer to a representative of the club instead of the entire club.

A common and practical equivalence relation that you are familiar with but not aware of is that of congruence. The congruence relation Rn says that two integers a and b are related if they have the same remainder when divided by n this is generally written as a=b mod n since this relation is an equivalence relation this means that a and b are in the same equivalent class. Remember that we can refer to an equivalence class by it's representative instead of the entire class.

modn partitions the set of integers Z into partitions/equivalence classes

a and b are in the same equivalence class when Z is partitioned by modn

Since we can refer to any member of an equivalence class, in the sense of mod n a is equivalent to b.

MAL

Notice that an integer would be in the same equivalence class as its remainder r since a%n = r and r %n = r. If an integer is very large we can instead refer to its remainder in arithmetic operations. This is why we do modn when we work with very large numbers in our code.

The rest of chapter 6 explores other ideas in MAL and their usefulness in various ways especially in proving interesting properties of integers.

What kind of relation is a function

for a function relation the b in the pair(a, b)is unique

A function is a set, this makes sense because they can be graphed where the pair (a, b) are coordinates. Functions can be represented as matrices and the action of a function on a point as matrix multiplication

Properties of a function vs properties of a relation

Surjection: for every output there is at least one i.e 1 or more input

Injection: for every output there is at most one i.e 0 or more input
bijection: for every output there is exactly one input

When there is a bijection we can talk of reversing the impact of a function i.e apply the inverse of that function

Bijections helps us compare the sizes of sets, two sets are the same size if there's is a bijection relation between them and this idea is used to define finite, infinite, countably infinite and uncountably infinite set.


### Chapter 8: Combinatorics
A way of counting the members of a finite set without knowing them.This is the branch of mathematics known as discrete maths. Introduces basic counting principles like rule of sum, rule of products (based on the discussion of the cardinality of finite sets in c7) and counting formulas like permutation, combination, binomial coefficients, binomial theorem.

Then it looks at more advanced counting techniques such as the pigeonhole principle  and Inclusion/exclusion

## My Take
Overall it is a perfect book for someone like me trying to make sense of the subject and Ideal for someone starting a career as a mathematician

The objectives and lookahead. In fact I used the objectives as a guide to ensure that I touched the essentials in the chapter summaries of this post.

It was long but not unreasonably so, a lot of examples, and illustrations

no solutions to the problems which makes it harder to walk through them as there's no feedback which is important for someone just trying to learn.

My interpretation of notation has greatly improved and I'm more comfortable writing notations myself. When I see a proof now I'm not just trying to understand it, I'm now also trying to map it to a proof technique, why it was conveniently proven this way, are there other ways to prove the same thing? and this has made reading heavily mathematically texts more fun to read.

## Advice to other readers

Many times while reading an example problem or a proof I tried to guess the approach

I recommend walking through all the example problems by yourself (I really enjoyed doing this and I've come to fall in love with the sound of my pencil on a white paper when scribbling equations) and at least the exercises in the chapter.

Because of the motive I had for reading this book and the tight deadline I had to finish this book I found myself often just concentrating on what I could apply to my work, forgetting to enjoy the pure beauty and wonder that mathematics offers, make it a point to enjoy it that's the only way to get through the chapters, maybe make a note or creed that you can read every time you pick up this book that reminds you of this.

Dr Brendan has a way of pushing readers to engage more with the text by posing questions in between. This can be both helpful and intimidating especially if this is your first time with mathematical theory. I usually read each chapter 3 times, first time —————-
