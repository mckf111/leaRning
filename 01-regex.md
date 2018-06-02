---
layout: post
title: "01-regex"
author: Wenhu
date: 2018-01-25
mathjax: yes
tags: R regex
categories: leaRning-fundamental
---

* content
{:toc}

> Fundamental introduction to regular expression (*regex* or *regexp* for short) in R. **Repost by indicating the source please!**

## Why I talk about regex

One week ago, I encoutered a problem when I was learning [The Art of R Programming](http://diytranscriptomics.com/Reading/files/The%20Art%20of%20R%20Programming.pdf) (page 35), I would like to use pattern match, that is regex, however, something unusual happened. I sought for help on `stackoverflow`, you could find the question [here](https://stackoverflow.com/questions/48239756/find-the-consecutively-matched-pattern-index) (*unfortunately duplicated*). The answer by [akrun](https://stackoverflow.com/users/3732271/akrun) invokes my interest on regex!

Regex is kind of independent but important concept that is almost implemented by every programming language. **Although this post is titled with "01", I suggest, you could come back learning this when you come across some concrete problems related, that would make you understand more clearly!**




## Regular Expression

Have you ever thought about the questions concerning about known patterns when you face bunch of biological data, most probably sequence information (such as basepairs and amino acids infos): Is there any [TATA box](https://en.wikipedia.org/wiki/TATA_box) inside the DNA sequence? May there be some [lncRNA](https://en.wikipedia.org/wiki/Long_non-coding_RNA) contained in the RNA? Or how many [zinc finger](https://en.wikipedia.org/wiki/Zinc_finger) motifs are there in the peptide chain? On the other hand, you would also probably need to solve the problem from time to time, like **replace** some texts compliant with fixed **patterns** with other new texts.

**Regex is nearly an invincible sword stabbing down all the pattern matching problems!**

> Regular expression (normally regex or regexp for short) is a series of consecutive characters that defines specific patterns for matching. It complies with certain syntax rules, when you use regex, the programming language will invoke related engines (built-in or third-party softwares) to search for pattern matched text.

## Flavors

Based on the concept before, you may think of regex as some pattern writing specification, be one and only, adopted by most programming languages. However, shit happens, there are dozens of different regex syntax (we call them flavors), what's more, several languages implement more than one out there, like R! The most two popular syntax flavors are BRE (Basic Regular Expression) / ERE (Extended Regular Expression) compliant with POSIX (Portable Operating System Interface for uniX) standards and PCRE (Perl Compatible Regular Expression), details on [regular expression in wiki](https://en.wikipedia.org/wiki/Regular_expression#POSIX_basic_and_extended).

**In R (version > 2.10.0), it adopts two flavors: [Ville Laurikari's TRE](https://laurikari.net/tre/about/) as default, [PCRE](http://www.pcre.org/original/doc/html/) as option.**

> In this tutorial, I will only introduce PCRE flavor, it is very popular and efficient, I will also strongly recommend you would also try it, simply by adding `perl = TRUE` in the pattern matching functions, details below.


## Common syntax

Fortunately, people are lazy! Although there are several flavors, people would never like to build the wheel from scratch, that would cause most part of these flavors share common syntax. Below I will introduce several **frequently used syntax in PCRE**. (adapted from [learn regex the easy way](https://github.com/zeeshanu/learn-regex#1-basic-matchers))

### Basic matching

"the" => The fat cat sat on **the** mat.

"The" => **The** fat cat sat on the mat. *（case-sensitive as default）*

### Meta characters

1. `.`（period）：wildcard character, it would match all **single** character except [newline](https://en.wikipedia.org/wiki/Newline).

    `".ar"` => The **car** **par**ked in the **gar**age.

2. `[...]`（character set）：You could use any character inside to match a **single** position, and **hyphen could be used to indicate range, e.g. [a-z] means each character between a and z, including them**.

    `"[Tt]he"` => **The** car parked in **the** garage.

3. `[^...]`（negated character set）：Every character inside would **not** be matched.

    `"[^c]ar"` => The car **par**ked in the **gar**age.

4. `+`, `*`, `?`（repetition）：
  * `+`：repeat for 1 or more times    `"c.+t"` => The fat **cat sat on the mat**.
  * `*`：repeat for 0 or more times    `"[a-z]*"` => T**he car parked in the garage** #21.
  * `?`：repeat for 1 or 0 times    `"[T]?he"` => **The** car is parked in t**he** garage.

5. `{n, m}`：Like Point 4, it restrain the repetition among n and m, including them. NOTE: `{n}` means n times only, and `{n, }` means greater than or equal to n times.

    `"[0-9]{2,3}"` => The number was 9.**999**7 but we rounded it off to **10**.0.

6. `()`（subpattern）：The pattern not characters inside should be matched exactly.

    `"(ar\si)"` => The c**ar i**s parked in the garage. *（`\s` means whitespace）*

7. `|`（alternation）：any one is OK.

    `"(T|t)he|car"` => **The car** is parked in **the** garage.

8. Anchors: `^` means the character followed must be the first in input, and `$` indicates the character before must be the last in input：

    `"^(T|t)he"` => **The** car is parked in the garage.

    `"(at\.)$"` => The fat cat. sat. on the m**at.**

### Shorthand character sets

| shorthand |                definition                |
| :-------: | :--------------------------------------: |
|    \w     | match all alphanumeric character: `[a-zA-Z0-9]` |
|    \W     |      Contrary to the above: `[^\w]`      |
|    \d     |        match all digits: `[0-9]`         |
|    \D     |      Contrary to the above: `[^\d]`      |
|    \s     | match all whitespace character, such as `[\t\n\r]` |
|    \S     |      Contrary to the above: `[^\s]`      |

### Lookaround

This is a very interesting and useful syntax you should know about.

Just suppose, if we want to locate the pattern X with subpattern Y around, how could we do that? You may directly match the pattern X, but how would you exclude the X without Y nearby? **This is the show time for "lookaround", we would look ahead and behind for pattern X, with eyes fixed on Y!**

* `X(?=Y)`（positive lookahead）：match the X before Y.

  `"(T|t)he(?=\sfat)"` => **The** fat cat sat on the mat.

* `X(?!Y)`（negative lookahead）：Not match the X before Y.

  `"(T|t)he(?!\sfat)"` => The fat cat sat on **the** mat.

* `(?<=Y)X`（positive lookbehind）：match the X behind Y.

  `"(?<=(T|t)he\s)(fat|mat)"` => The **fat** cat sat on the **mat**.

* `(?<!Y)X`（negative lookbehind）：Not match the X behind Y.

  `"(?<!(T|t)he\s)(cat)"` => The cat sat on **cat**.

> Note: the pattern Y in "lookaround" would not be captured by the searching engines, this could be beneficial under some circumstances, refer to [my question](https://stackoverflow.com/questions/48239756/find-the-consecutively-matched-pattern-index).

Above are the commonly used PCRE syntax, if you want to know the details, please go to [PCRE syntax](http://www.pcre.org/original/doc/html/pcresyntax.html).

## How to use regex in R

There are normally two ways doing pattern match in R, one is using the functions in `base` package, another is using a series of functions in `stringr` package, I will only introduce the former, however, I strongly suggest you to discover the beauty of the latter, you could get a quick start in [R for data science - Strings](http://r4ds.had.co.nz/strings.html). (adapted from [Regular Expressions with The R Language](https://www.regular-expressions.info/rlanguage.html))

### Matching

* `grep` function：* The meaning of "grep" is "**g**lobal search for **r**egular **e**xpression and **p**rint matching lines", it is the most important function and the model for others. *The first argument is pattern, the second in the input string, waiting for being matched. When you specify `value=FALSE`, it will return the position of each matched element, `value=TRUE` would return you exactly the elements matched.

```
> grep("a+", c("abc", "def", "cba a", "aa"), perl=TRUE, value=FALSE)
[1] 1     3       4
> grep("a+", c("abc", "def", "cba a", "aa"), perl=TRUE, value=TRUE)
[1] "abc" "cba a" "aa"
```

* `grepl` function："l" stands for "logical", you could then guess what will be returned.

```
> grepl("a+", c("abc", "def", "cba a", "aa"), perl=TRUE)
[1] TRUE  FALSE TRUE  TRUE
```

* `regexpr` function：The above two function will only tell you which element has been matched, but how could we know the exact matching location inside the element? You need to use `regexpr` or `gregexpr`. They have the same arguments setting as `grep`. `regexpr` will return the **first** matching location when searching from left to right with the pattern you give! `-1` stands for no match.

```
> regexpr("a+", c("abc", "def", "cba a", "aa"), perl=TRUE)
[1]  1 -1  3  1
attr(,"match.length")
[1]  1 -1  1  2
attr(,"useBytes")
[1] TRUE
```
> This fuction will return a vector which has the same length with input, containing the first matching locations, the vector has two attributes, the first means the length of matched strings, like the fourth matched text here is `"aa"`, so the length is `2`, you don't need to know the second right now.

* `gregexpr` function：It has an additional "g" compared to `regexpr`, yes, it is a globally matching function, it will return a list, each element inside contain the **overall** matched location for each input.

```
> gregexpr("a+", c("abc", "def", "cba a", "aa"), perl=TRUE)
[[1]]
[1] 1
attr(,"match.length")
[1] 1
attr(,"useBytes")
[1] TRUE

[[2]]
[1] -1
attr(,"match.length")
[1] -1
attr(,"useBytes")
[1] TRUE

[[3]]
[1] 3 5
attr(,"match.length")
[1] 1 1
attr(,"useBytes")
[1] TRUE

[[4]]
[1] 1
attr(,"match.length")
[1] 2
attr(,"useBytes")
[1] TRUE
```

* `regmatches` function：This one is used for showing the **exact matched part** of each input elements. *It needs two arguments: one is the **pattern**, another is the **value returned by `regexpr` or `gregexpr`**, because it contains the matched location and matched length*.

```
> x <- c("abc", "def", "cba a", "aa")
> m <- regexpr("a+", x, perl=TRUE)
> regmatches(x, m)
[1] "a"  "a"  "aa"

> m <- gregexpr("a+", x, perl=TRUE)
> regmatches(x, m)
[[1]]
[1] "a"

[[2]]
character(0)

[[3]]
[1] "a" "a"

[[4]]
[1] "aa"
```

### Replacement

* `sub` and `gsub` functions：Obviously, they need three arguments: **pattern, replacement and input**. Please check examples below to figure out the differences between `sub` and `gsub`.

```
> sub("(a+)", "z", c("abc", "def", "cba a", "aa"), perl=TRUE)
[1] "zbc"   "def"   "cbz a" "z"    
> gsub("(a+)", "z", c("abc", "def", "cba a", "aa"), perl=TRUE)
[1] "zbc"   "def"   "cbz z" "z"
```

* One more efficient way for replacement is to use `regmatches` function, you could assign a vector containing the **replacements** to the `regmatches`, then it will change the **original texts** automatically.

```
> x <- c("abc", "def", "cba a", "aa")
> m <- gregexpr("a+", x, perl=TRUE)
> regmatches(x, m) <- list(c("one"), character(0), c("two", "three"), c("four"))
> x
[1] "onebc"       "def"         "cbtwo three" "four"
```

## Summary

Although regex has been used widely, you still need a while to get used to this scattered syntax, after you pass this period, you will enjoy most of your time using it! This tutorial is just a brief introduction to some important part of regex, if you want more, and you should, I recommend the following resources:

1. [regex-info](https://www.regular-expressions.info/)
2. [regex-online-learning](https://regexone.com/)
3. [Regular-Expressions-Cookbook](https://github.com/shihyu/Regular_Expressions/blob/master/O%27Reilly-Regular%20Expressions%20Cookbook(2nd%20Edition).pdf)

**Hope you will find a match with regex!**

> Please subscribe [RSS](http://bioinfostar.com/feed.xml) if you wanna receive my newest post! 