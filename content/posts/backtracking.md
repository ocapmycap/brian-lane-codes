---
title: "Backtracking"
date: 2023-01-27T21:35:00-08:00
draft: true
---

I recently had to solve an interview problem that used [backtracking](https://en.wikipedia.org/wiki/Backtracking) for an optimal solution. I got lost in trying to address it because of complexity, and when I learned the answer I was still confused. I spent some time illustrating it (with my very poor Lucid Chart skills) and thought the process helped immensely so I am recreating it here. 

The problem exists on [LeetCode](https://leetcode.com/problems/combination-sum/). It reads as follows: 

>Given an array of **distinct** integers `candidates` and a target integer `target`, return *a list of all **unique combinations** of* `candidates` *where the chosen numbers sum to* `target`*.* You may return the combinations in **any order**.
>
>The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.
>
>The test cases are generated such that the number of unique combinations that sum up to `target` is less than `150` combinations for the given input.
>
>**Example 1**
>
>```swift
>Input: candidates = [2,3,6,7], target = 7
>Output: [[2,2,3],[7]]
>Explanation:
>2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
>7 is a candidate, and 7 = 7.
>These are the only two combinations.
>```

The first solution in that output array illustrates some of the complexity of the problem. Though the input array `candidates` only contains one 2, you can re-use any member any number of times to get to your outcome. 

There are some hints here about using recursion. First, it seems obvious that there can be multiple solutions. Second, shaving off elements one-by-one results in outputs that you can glue together. That is, say we split the array into `[2]` and `[3,4,7]`. We could run our recursive algorithm on the final three indices and get back an array of array, including all possible combinations of those three. A simple combination of those three remaining members would be (n!) / [(150!) x (n - 150)! + ... (n!) / [(1!) x (n - 1)! which is a metric ton. You could go through adding `[2]` to each of those combinations and only keep the ones that add up to the target, 7. 

But the backtracking approach allows you to short circuit incorrect results ahead of time. 

Let's start with an index of `0`, an empty multidimensional array for `results` and a `sum` initialized to 0. We also have an empty array `current` that represents the possible answer we are building up. 

<img src="/Users/brianlane/Documents/Archives/Screenshots/Screenshot 2023-01-28 at 5.20.29 PM.png" alt="Screenshot 2023-01-28 at 5.20.29 PM" style="zoom:50%;" />

First, let's assure ourselves that `candidates` is not empty (it's not), then check if our sum is equal to our target (it's not). Base cases aside, let's get into it.

We now want to cycle through each index. For the member at that index, check if it's less than or equal to `target - sum` to ensure we haven't gone wrong yet. If not, add the member value to `sum` and append the member value to the empty `current` array.

<img src="/Users/brianlane/Library/Application Support/typora-user-images/Screenshot 2023-01-28 at 5.24.23 PM.png" alt="Screenshot 2023-01-28 at 5.24.23 PM" style="zoom:50%;" />

Now we enter recursion. Let's call this function again with the same index, but with the updated sum.

Base case checks, then we get into some real businesss. We're still at our same index, and it's less than or equal to `target - sum`. Add it to the `sum` and `current` and keep going. 

<img src="/Users/brianlane/Library/Application Support/typora-user-images/Screenshot 2023-01-28 at 5.26.58 PM.png" alt="Screenshot 2023-01-28 at 5.26.58 PM" style="zoom:50%;" />

Recurse again. We add another 2 and then on our next pass we see that we hit a snag. <img src="/Users/brianlane/Library/Application Support/typora-user-images/Screenshot 2023-01-28 at 5.27.49 PM.png" alt="Screenshot 2023-01-28 at 5.27.49 PM" style="zoom:50%;" />

We can't add any more 2s here, so we start rewinding. Remove 2s from our `sum` and `current` variables until our for loop can progress.

 <img src="/Users/brianlane/Library/Application Support/typora-user-images/Screenshot 2023-01-28 at 5.30.57 PM.png" alt="Screenshot 2023-01-28 at 5.30.57 PM" style="zoom:50%;" />

We get back down to this before progressing by increasing our index. Sure enough, here we have a 3, which we can add to our current result. Once we recurse from this state, we realize in our next base case check that we have a winner. 

![Screenshot 2023-01-28 at 5.32.45 PM](/Users/brianlane/Library/Application Support/typora-user-images/Screenshot 2023-01-28 at 5.32.45 PM.png)

Note here that results has an array. The recursion stack will start unwinding as we try to add numbers (`[2,2,3,3]` is too much, so the stack will rewind to `[2,2]`, then `[2]`, then empty.) At this point, the original for loop will increment and it will start testing with a new array `[3]`. The loop will progress trying to create satisfactory results before eventually winding down to being empty, and the for loop incrementing to point at the 7, which matches the target alone. 

The end results array is [[2,2,3], [7]].

Here's a coded up version in Swift.

```swift
struct CombinationSum {
    static func combinationSum(_ candidates: [Int], _ target: Int) -> [[Int]] {
        var current = [Int]()
        var sum = 0
        var results = [[Int]]()
        let sorted = candidates.sorted()
        combinationSumBacktracking(sorted, target, 0, &results, &current, &sum)
        return results
    }
    
    static func combinationSumBacktracking(
        _ candidates: [Int],
        _ target: Int,
        _ index: Int,
        _ results: inout [[Int]],
        _ current: inout [Int],
        _ sum: inout Int
    ) {
        guard index < candidates.count else { return }
        if sum == target {
            results.append(current)
            return
        }
        
        for j in index..<candidates.count {
            guard candidates[j] <= target - sum else { return }
            sum += candidates[j]
            current.append(candidates[j])
            combinationSumBacktracking(
                candidates,
                target,
                j,
                &results,
                &current,
                &sum
            )
            sum -= current.removeLast()
        }
    }
 }
```

Note that the array in this example was already sorted. In the code above, we sort the array so that we can always guarantee an early exit once our `sum` grows larger than the target. 

This is part of a larger class of problems called [constraint satisfaction problems)](https://en.wikipedia.org/wiki/Constraint_satisfaction_problem). 

