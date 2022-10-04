---
title: "[LeetCode] 49. Group Anagrams"
excerpt: "LeetCode 49번 문제와 솔루션"

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
Given an array of strings `strs`, group **the anagrams** together. You can return the answer in **any order**.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

**Example 1:**
```
Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
```
**Example 2:**
```
Input: strs = [""]
Output: [[""]]
```
**Example 3:**
```
Input: strs = ["a"]
Output: [["a"]]
```
**Constraints:**
- `1 <= strs.length <= 104`
- `0 <= strs[i].length <= 100`
- `strs[i]` consists of lowercase English letters.

---
## **Solution**
```java
class Solution {
    public List<List<String>> groupAnagrams(String[] strs) {
        Map<String, List<String>> map = new HashMap<String, List<String>>();
        for (String str : strs) {
            char[] arr = str.toCharArray();
            Arrays.sort(arr);
            String keyStr = String.valueOf(arr);
            if (map.containsKey(keyStr) == false)
                map.put(keyStr, new ArrayList<String>());
            map.get(keyStr).add(str);
        } return new ArrayList<List<String>>(map.values());
    }
}
```
## **Explanation**
- 두 문자열이 `Anagram`인지 확인할 기준이 필요한데, 기준이 되려면 두 문자열 속에 있는 문자를 모두 가져야 하고, 순서 또한 같아야 한다.
- `문자 배열 arr`을 선언하고 `배열 strs`의 문자열을 문자 배열로 만들어 할당해주고 이를 정렬한후, `변수 keyStr`에 `배열 arr`의 값을 할당해주어 위의 기준을 만족하는 기준을 만들어준다.
```java
char[] arr = str.toCharArray();
Arrays.sort(arr);
String keyStr = String.valueOf(arr);
```
- 기준은 겹칠 수 없지만, 그 기준에 해당하는 문자열은 여러개 존재할 수 있다는 점에서 `자료구조 map`을 떠올릴 수 있다.
```java
Map<String, List<String>> map = new HashMap<String, List<String>>();
```
- `if 조건문`을 사용하여 `자료구조 map`에 keyStr이 겹치지 않도록 키로 넣어주고, 값으로는 빈 리스트를 생성해 넣어준다.
- 그리고 기준에 해당되어 Anagram으로 판단이 되면 `배열 strs`의 문자열을 map에 넣어주도록 한다.
```java
if (map.containsKey(keyStr) == false)
    map.put(keyStr, new ArrayList<String>());
map.get(keyStr).add(strs[0]);
```
- 위 과정을 반복해야 하므로 `for 반복문`을 선언하여 반복을 수행해준다.
```java
Map<String, List<String>> map = new HashMap<String, List<String>>();
for (String str : strs) {
    char[] arr = str.toCharArray();
    Arrays.sort(arr);
    String keyStr = String.valueOf(arr);
    if (map.containsKey(keyStr) == false)
        map.put(keyStr, new ArrayList<String>());
    map.get(keyStr).add(str);
}
```
- `자료구조 map`의 값들만 리스트로 만들어 반환하도록 한다.
```java
return new ArrayList<List<String>>(map.values());
```