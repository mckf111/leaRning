---
layout: post
title: "02-Main Data Structures"
date: 2018-04-25
categories: leaRning-fundamental
tags: R
author: Wenhu
mathjax: true
---

* content
{:toc}

> Brief introduction to the main data structures in R, part of contents adapted from *Advanced R*, by *hadley Wickham*. **Repost by indicating the source please!**

## Data Structures

When you use your computer to handle all kinds of tasks, like writing, drawing, surfing the internet, actually, they are just as simple processes as you give **data** and **command** to computer, then it manipulates in its own way and gives back new data to you. During this, the programming language behaves like a google language translator between you two.





Computer is designed to manipulate different kinds of huge data, if we just handle that to computer, it will surely cause chaos! Normally we will find and comprehend the relationships among data before, so as to **facilitate the storage and manipulation in computer later.** We need to arrange the data properly and efficiently, this is the origin of data structures. **Data structures include not only the data themselves, but also the relationships among them and a series of manipulation protocols.**

In almost all advanced programming language, we don't need to worry about making data structures by hand, all of them are built-in properties. Let us have a look, what is the main data structures in R: 

|  |         Homogeneous elements          |   Heterogeneous elements   |
| :-------: | :-------------------------: | :-----------: |
|    1D (one-dimentional)     | Atomic vector | Recursive vector = List |
|    2D     |       Matrix       | Data frame |
|    >=3D     |       Array       |  |

At first glance, data structures in R consist of two 1D vectors and their derivatives, vectors are really the cornerstones of R!

## Vector - Heart of R

Most of us may know the word as a geometric object that has both magnitude and direction, when we learned in mathematics or physics. It is normally shown graphically as an arrow in a coordinate system. For example, in a n-dimentional coordinate, a vector starting from the origin can be expressed as `(X1, X2, ... , Xn)`.

However, in programming language, vectors are firstly used as a carrier for numbers, like `(1, 2, 3, 4, 5)` is just a vector containing 5 numbers, it has similarities with the mathematical definition in certain degree. Now, the usage of vectors has broadened largely, they can contain (carry) anything!

When all the elements in a vector belongs to the same data type (logic, integer, float, character, etc.), we call it **atomic vector**. While the types are different, we call it **recursive vector, aka List.** **Atomic** means any element in this vector can not be divided into sub-vector, it is the ultimate atom. While the element in **list** still could be atomic vector or list, even array or data frame, which can contain many more internal elements, then divided and divided, so we call it **recursive**.


```r
dbl.atom <- c(1, 2, 3) # Float type, aka numeric type in R.
int.atom <- c(1L, 2L, 3L) # Integer in R only end with L!
typeof(dbl.atom)
```

```
## [1] "double"
```

```r
typeof(int.atom)
```

```
## [1] "integer"
```


```r
alist <- list(1:9, c("I", "LOVE", "U"), c(TRUE, FALSE), c(1.2, 3.4, 5.6))
str(alist)
```

```
## List of 4
##  $ : int [1:9] 1 2 3 4 5 6 7 8 9
##  $ : chr [1:3] "I" "LOVE" "U"
##  $ : logi [1:2] TRUE FALSE
##  $ : num [1:3] 1.2 3.4 5.6
```

> Vectors, as **containers**, are **dynamic**, you could insert or delete the elements inside whenever you want. They are also **ordered**, which means any elment has unique index, like ID.

### Properties of R Vectors

* **Vectorization**: It is one of the **highlights** in R! Generally, R behaves as functional programming language more than object-oriented language, almost any manipulation bases on functions, even operators. Almost all the functions can work on all the elements in vectors separately, if we just pass the vectors into the functions. This facilitates the code readability and sustainability largely!


```r
dbl.atom == int.atom 
```

```
## [1] TRUE TRUE TRUE
```

```r
"=="(dbl.atom, int.atom) # Astonishing?
```

```
## [1] TRUE TRUE TRUE
```

```r
identical(dbl.atom, int.atom) # Surprising?
```

```
## [1] FALSE
```

* **Recycling**: When more than one vector are passed to a function, the vectors with shorter length will recycle their elements to compensate the difference with longer vectors. **This also indicate all the scalars in R are actually atomic vectors with length 1.**


```r
c(1, 2) + c(3, 4, 5, 6, 7) # c(1, 2) -> c(1, 2, 1, 2, 1)
```

```
## Warning in c(1, 2) + c(3, 4, 5, 6, 7): longer object length is not a
## multiple of shorter object length
```

```
## [1] 4 6 6 8 8
```

```r
1 + 1:9 # 1 is actually c(1)
```

```
## [1]  2  3  4  5  6  7  8  9 10
```

* **Filtering**: We can set filters on vectors directly to get the sub-vectors.


```r
emotion <- c("LOVE", "HATE")
my.emo.2u <- emotion[emotion > "I"]
my.emo.2u
```

```
## [1] "LOVE"
```

> Because other main data structures come from the derivation of vector, all the three properties are also applicable!

## Matrix and Array

They are relatively simple, only adding one more attribute *dimention (dim)* on atomic vector.


```r
my.matrix <- matrix(1:9, nrow = 3)
my.matrix
```

```
##      [,1] [,2] [,3]
## [1,]    1    4    7
## [2,]    2    5    8
## [3,]    3    6    9
```

```r
dim(my.matrix)
```

```
## [1] 3 3
```

```r
my.array <- array(1:12, c(2, 3, 2)) # dimention in the vector inside
my.array
```

```
## , , 1
## 
##      [,1] [,2] [,3]
## [1,]    1    3    5
## [2,]    2    4    6
## 
## , , 2
## 
##      [,1] [,2] [,3]
## [1,]    7    9   11
## [2,]    8   10   12
```

```r
dim(my.array)
```

```
## [1] 2 3 2
```

## Data frame

At first glance, data frame is like a matrix, with rows and columns, however, the elements inside are heterogeneous. Actually, data frame is a list, **of which each column is an element**, the only limitation for the list is each element has **the same length**!


```r
xiaochou <- data.frame(x = c("一", "杯", "敬", "明", "天"), y = c("一", "杯", "敬", "过", "往"))
xiaochou
```

```
##    x  y
## 1 一 一
## 2 杯 杯
## 3 敬 敬
## 4 明 过
## 5 天 往
```

```r
str(xiaochou)
```

```
## 'data.frame':	5 obs. of  2 variables:
##  $ x: Factor w/ 5 levels "杯","敬","明",..: 5 1 2 3 4
##  $ y: Factor w/ 5 levels "杯","过","敬",..: 5 1 3 2 4
```

## Factor

At the end, we should not ignore one important buddy - factor, which could be regarded as a integer-element atomic vector, with two more attributes: class type - `factor` and `levels`. Factor is mostly used as storage for discrete data, which has important application in data frame manipulations.


```r
x <- factor(c(3, 4, 4, 3))
x
```

```
## [1] 3 4 4 3
## Levels: 3 4
```

```r
class(x)
```

```
## [1] "factor"
```

```r
levels(x)
```

```
## [1] "3" "4"
```

```r
## x is actually c(1L, 2L, 2L, 1L)，level1 = 3，level2 = 4
as.double(x)
```

```
## [1] 1 2 2 1
```

## Summary

Vectors make victory!

> Please subscribe [RSS](http://bioinfostar.com/feed.xml) if you wanna receive my newest post!