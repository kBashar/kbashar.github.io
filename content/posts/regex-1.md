---
title: "How \* and + works in regex"
date: 2018-09-12
draft: false
---

+, \* both are quantifiers. That is they don't themselves match any pattern but check for repetition or presence of the pattern they are used after. One example will clear things up.

Assume, we want to match some numbers

*   9
*   90
*   234
*   2275258292335728520

To match numbers we can use character class `[0-9]` or `\d`, both work the same. but there is one problem, `\d` or `[0-9]` both matches number with one digit only. i.e. will match 9 only not other numbers with more digits. So to match two digit number our regex should be `\d\d`, for three digit numbers `\d\d\d` and so on. You see the problem?

Firstly, it's tiring to write repeating characters and secondly what if we don't know the count of digits of the number beforehand. Here comes the quantifiers to rescue.

\+ -> matches at least one and more of the pattern it's used after.\`
\* -> matches none or more of the patterns it's used after.\`

Going back to the previous example, we want to match numbers be it one digit or more, we make our regex `\d+` i.e. one + plus after digit matching pattern `\d`. Now it matches all of the numbers.

Now let's imagine a scenario where we want to match `a` that has no `b` after it or lots of `b`.

*   a
*   ab
*   abbbb

We have to write a regex that matches all of the above. So if we write a regex `ab+` we fail to match the lone `a` cause `+` matches one or more `b`, not none. Here we use `*` . it matches none or more of the pattern. So regex `ab*` will match all of the above three strings including the first one where there is no `b`.

Hope this demystify `*` and `+` for you.