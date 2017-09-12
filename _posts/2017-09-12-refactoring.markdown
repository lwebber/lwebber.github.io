---
layout: post
title:  "Refactoring"
date:   2017-09-12 23:28:25 +0000
---


Nothing gives me greater pleasure than a good refactor.

As an English major, writer, and teacher, I've developed an editor's mindset over the years and love to see text streamlined, made less redundant, and, well "elegantized", if I can turn "elegant" into a verb.

One of the fun parts of writing a haiku is getting your ideas across in a bare bones skeleton of syllables. There's just something delicious about a well-wrought turn of phrase, something simple that conveys something powerful:

> I kill an ant<br>
and realize my three children<br>
have been watching.<br>
--Kato Shuson

Though we're only give 3 lines--5 syllables, then 7, then another 5-- a whole scene unfolds. What do the children's faces look like? Are their hearts forever marred by this simple act of violence? How will the father explain?

Refactoring has similar power. A whole story unfolds from a few simple line of code. Consider the following:

```
def prime?(int)
  range = (2..int).to_a
  range.select! { |num| int%num == 0 }
  range.length==1
end
```

I tackled this problem early on in my web development bootcamp. Having done similar problems in Java as an AP Computer Science teacher, I had some familiarity with algorithms for prime numbers, but because I was a self-taught programming teacher, I had been stuck with the limited Java library given to us by the College Board. Studying Ruby explosed me to some utterly blissful coding experiences-- ways to impart the same ideas without all the extra code--like return statements and if's.

A prime number is only divisible by itself and 1. My approach her was to create a range of integers from 2 up to the integer int. The expectation would be that there is only one number within that range divisible by the integer in question-- the integer itself. For the second line, I used the select! enumerator to find the number in the range divisible by the integer. This is accomplished by using the % operator to find a remainder equal to zero. I had originally used the select operator (without the "modify in place" exclamation point), but refactored because there was no need to have an intermediary local variable cluttering up the code.

Logically, if there's only one factor (the integer itself) in this selection, the number is prime.

One could tackle it another way, by making the range from 2 up to but not including the integer and check for a length of zero, but that would introduce an "int - 1" piece of code which is actually unnecessary. If you're checking for length, why not check for 1 instead of 0 and remove the cluttering and confusing "-1"?

The parts I find especially delicious:

Method Chaining:
Make a range and turn it into an array in the same step

```
range = (2..int).to_a
```

Modify in Place:
```
select!
```
Doesn't the exclamation point make you happy?

Replace Do-End with Braces:
```
{ |num| int%num == 0 }
```
So much more pleasant all on one line.

Now, let's try this:

```
def prime?(int)
  (2..int).to_a.select! { |num| int%num == 0 }.length==1
end
```

Huzzah! It's my code haiku.

