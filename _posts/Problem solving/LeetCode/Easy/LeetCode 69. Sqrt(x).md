---
title: "[LeetCode] 69. Sqrt(x) with java"
excerpt: "LeetCode 69번 문제와 솔루션"

categories:
  - LeetCode
tags:
  - 

toc: true
toc_sticky: true
 
date: 2022-08-29
last_modified_at: 2022-08-29
---
# 69. Sqrt(x)
## **Problem**
Given a non-negative integer `x`, compute and return *the square root of* `x`.

Since the return type is an integer, the decimal digits are **truncated**, and only **the integer part** of the result is returned.

**Note**: You are not allowed to use any built-in exponent function or operator, such as `pow(x, 0.5) or x ** 0.5`.


**Example 1:**
```
Input: x = 4
Output: 2
```
**Example 2:**
```
Input: x = 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since the decimal part is truncated, 2 is returned.
```
<!-- **Example 3:**
```

``` -->
**Constraints:**
- 0 <= x <= 2^31 - 1

---
## **Solution**
```java
class Solution {
    public int mySqrt(int x) {
      if (x == 0) return 0;
	    long i = x;
    	while(i > x / i)  
	      	i = (i + x / i) / 2;	    	
	    return (int)i;
    }
}
```
## **Explaination**
-