---
title: Regular Expressions
date: 2021-09-05T09:48:08+01:00
anchor: regular-expressions
weight: 265
---

Explaining [regular expressions][1] used in the text search is beyond
the scope of these docs. There is at least one [book][2] (of many)
devoted to the subject. Use `(?i)` for case insensitive search, `.`
matches any character once, `.?` matches any character once or not at
all, `.+` matches any character one or more times, `.*` matches any
character any times or not at all. Use `.+?` or `.*?` for reluctant
versions. Use `\b` for a word boundary, `\d` for a digit, `\s` for a
white space character, `\w` for a word character, so `\w+` matches
words. Use `[abc]` to match a set of characters, or `[a-z]` for a
range, so `\w*[aeiou]+\w*` should match any word with at least one
vowel in it.

 [1]: https://en.wikipedia.org/wiki/Regular_expression
 [2]: https://www.oreilly.com/library/view/mastering-regular-expressions/0596528124/
 
