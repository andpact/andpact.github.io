---
title: "[LeetCode] 48. Rotate Image"
excerpt: "LeetCode 48번 문제와 솔루션"

categories:
  - LeetCode
tags:
  - 

toc: true
toc_sticky: true
 
date: 2022-10-04
last_modified_at: 2022-10-04
---
## **Problem**
You are given an `n x n` 2D `matrix` representing an image, rotate the image by **90** degrees (clockwise).

You have to rotate the image **in-place**, which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.


**Example 1:**

![image](https://user-images.githubusercontent.com/107045604/193800403-253661a3-0c79-4abe-a6a9-57fc55e5018a.png)
```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]
```
**Example 2:**

![image](https://user-images.githubusercontent.com/107045604/193800495-60403b95-803e-4423-bcae-6575e9e02773.png)
```
Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

**Constraints:**
- `n == matrix.length == matrix[i].length`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`

---
## **Solution**
```java
class Solution {
    public void rotate(int[][] matrix) {
        for (int i = 0; i < matrix.length / 2; i++) {
            int[] tmp = matrix[i];
            matrix[i] = matrix[matrix.length - i - 1];
            matrix[matrix.length - i - 1] = tmp;
        } for (int i = 1; i < matrix.length; i++)
            for (int j = 0; j < i; j++) {
                int tmp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = tmp;
            }
    }
}
```
## **Explanation**
- 2차원 배열을 시계방향으로 90도 돌린다는 것은 배열을 `위아래로 뒤집고`, 왼쪽 상단과 오른쪽 하단을 연결하는 선을 기준으로 `대칭으로 뒤집는 것`과 같다.
- 먼저 `2차원 배열 matrix`를 위아래로 뒤집어주기 위해 `for 반복문`을 작성한다.
```java
for (int i = 0; i < matrix.length / 2; i++) {
    int[] tmp = matrix[i];
    matrix[i] = matrix[matrix.length - i - 1];
    matrix[matrix.length - i - 1] = tmp;
```
- 다음-으로 `2차원 배열 matrix`를 왼쪽 상단과 오른쪽 하단을 연결하는 선을 기준으로 대칭으로 뒤집기 위해 `for 반복문`을 작성한다.
- 첫 번째 `for 반복문`의 `변수 i`가 0부터 시작될 필요가 없으므로 1로 초기화해준다.
```java
for (int i = 1; i < matrix.length; i++)
    for (int j = 0; j < i; j++) {
        int tmp = matrix[i][j];
        matrix[i][j] = matrix[j][i];
        matrix[j][i] = tmp;
    }
```