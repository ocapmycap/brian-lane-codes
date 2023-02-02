---
title: "First Bad Version"
date: 2023-01-31T21:33:39-08:00
draft: true
tags: [leetcode, interviewing, binary-search]
categories: [leetcode, interviewing]
---

Solving [First Bad Version](https://leetcode.com/problems/first-bad-version/description/) requires familiarity with binary search. Basically, the fastest way to find something (with an asymptotic runtime of *log n*) is to look at the middle of your collection, then go to the middle of either side of it depending on if what you are looking for is less than or greater than the new item at the index. Repeat until you find it or run out of indices. Note, this only works in sorted collections. 

> You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.
>
> Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one, which causes all the following ones to be bad.
>
> You are given an API `bool isBadVersion(version)` which returns whether `version` is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

```swift
/**
 * The known API is defined in the parent class VersionControl.
 *     func isBadVersion(_ version: Int) -> Bool{}
 */
class Solution : VersionControl {
    func firstBadVersion(_ n: Int) -> Int {
      	// answer here
    }
}
```

We're doing with a sorted collection `1...n` so we should be tipped off that binary search is the way to go. We just need a mechanism for tracking our lower and upper bounds. 

```swift
var upper = n
var lower = 1
```

Now we need to search through the collection by determining the midpoint.

```swift
while lower < upper {
  let mid = (upper + lower) / 2
  if isBadVersion(mid) {
   	upper = mid
  } else {
    lower = mid + 1 // *
  }
}
```

*It's important to increment that lower assignment by 1 so you don't `while` loop forever.

Pretty straightforward. Here's the whole solution. 

```swift
    func firstBadVersion(_ n: Int) -> Int {
        var lower = 1
        var upper = n 
        while lower < upper {
            let mid = (upper + lower) / 2
            if isBadVersion(mid) {
                upper = mid
            } else {
                lower = mid + 1
            }
        }
        return upper
    }
```

