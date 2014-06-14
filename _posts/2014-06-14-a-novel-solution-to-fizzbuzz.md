---
layout: post
title: "A novel solution to FizzBuzz"
category:
tags: [clojure]
---
{% include JB/setup %}

I've always been mildly interested in [the FizzBuzz problem][wikipedia], which is a fairly simple test of one's programming ability in a given language; the task is to generate a list of the first 100 "fizz buzz" numbers: if the number is divisible by 3 (and not 5), it's "fizz," divisible by 5 (and not 3) it's "buzz," and if it's divisible by both 3 and 5, it's "fizz-buzz." It's a simple task in any language, and I've always been interested in seeing how concise a solution to the FizzBuzz problem can be.

[wikipedia]: http://en.wikipedia.org/wiki/Fizz_buzz#Other_uses

The obvious/canonical solution to this problem is a simple if-then-else statement, like this:

{% gist 5fbb82743bfb9b534628 %}

In a functional programming language like Clojure, one might prefer to map a function over the range 1-100 that returns "FizzBuzz," "Fizz," "Buzz" or the number itself, like this:

{% gist e9b9b758d8f00d35b180 %}

This approach is simple enough, but I don't like the part that checks whether `x % 15 == 0`. I prefer the kind of FizzBuzz solution that adds "Fizz" first if the number is divisible by 3, then adds "Buzz" if the number is divisible by 5, and so the numbers that are divisible by both 3 and 5 will automatically be "FizzBuzz" without having to add another step. I was thinking about functional programming approaches to this problem, and it occurred to me that one could take a list of the numbers (indices), as tuples with empty strings, and map over the list three times:

1. Add "Fizz" to the string if its index is divisible by 3.
2. Add "Buzz" to the string if its index is divisible by 5.
3. Return either the string or the index (if the string is still empty).

{% gist 6640d5c4c19194d8e214 %}

That's nice, but I still don't like the idea of having to map over the entire sequence three times. We should be able to write a small function that "fizz-buzzes" a number in one pass: this function would basically follow the same three steps, but in a simplified form:

1. Define a string consisting of "Fizz" if the number is divisible by 3 plus "Buzz" if the number is divisible by 5.
2. Is the string empty? Then return the number. Otherwise return the string.

{% gist bd31e3ac8608c1f01fa9 %}

This solution takes advantage of the convenient semantics of `nil` in Clojure. Each `when` statement returns either "Fizz"/"Buzz" or `nil`. A lot of Clojure functions are smart enough to know what to do with `nil`, and `str` is one of them; so, if we pass in 20, for instance, the evaluation of `(str nil "Buzz")` returns "Buzz" rather than throwing an error.

Is this solution really "better"? I don't know -- it's all subjective. Technically, there are still 3 steps -- we're checking for divisibility by 3 and 5 (steps 1 & 2) and stringifying it in terms of "fizz" and "buzz" in the process, and then checking whether the string is empty (step 3) and returning either a non-empty string or the original number. I haven't tested the difference in performance (which I'm sure is negligible), but I would guess that this last version is slightly faster because instead of doing a 3rd modulus operation to see if the number is divisible by 15, we're just checking to see whether a string is empty, which I would think would be a quicker operation.

Comment below if you have an interesting/novel approach to Fizz-Buzz!

P.S. I haven't forgotten about the next installment of "\_why's (poignant) guide to ruby in Clojure" -- it's coming, I promise!