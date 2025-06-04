---
layout: post
title: "Sliding Window Technique : Patterns & Use Cases"
slug: "sliding-window-technique"
date: 2025-06-04
author: Anubhav Srivastava
tags: [algorithms, sliding window]
---

#### What is Sliding Window technique?
Sliding window is a technique used for solving problems involving arrays, lists, or strings by processing a fixed-size portion of the data at a time. It's often used to efficiently find subarrays, substrings, or windows that meet a specific criteria. It is mostly used when the solution to the problem lies in a contiguous part of the data that is being processed.

Let's take a classic example to illustrate how sliding window helps in reducing the time complexity while solving problems.

**Problem :** *Suppose we’re given an array and asked to find the maximum sum of any subarray of size `k`.*

**Bruteforce Solution :** A straightforward brute-force approach would involve iterating through all possible subarrays of size `k`, calculating the sum for each one from scratch. This results in a time complexity of O(n \* k), which becomes inefficient as the array grows larger.

**Sliding Window Approach :** By using the sliding window technique, we can reduce the time complexity from O(n * k) to O(n). 
The concept is straightforward yet highly effective: At first, we begin by calculating the sum of the first window of size k. 
As we move the window forward one element at a time, we subtract the element that leaves the window and add the new element that enters. 
This allows us to update the sum in constant time, without recalculating the entire window each time. This approach minimizes redundant computations and provides a much more efficient and scalable solution for problems involving contiguous subarrays or substrings.

***Now with the help of some problems, let us try to understand the common Sliding Window patterns.***

---

#### 🧩 Pattern 1: Fixed-Size Sliding Window

##### 📘 Problem: Maximum Average Subarray I (Leetcode 643)

**Link:** [https://leetcode.com/problems/maximum-average-subarray-i/](https://leetcode.com/problems/maximum-average-subarray-i/)

##### 📝 Problem Statement

Given an integer array `nums` consisting of `n` elements and an integer `k`, find the **maximum average value** of any contiguous subarray of length `k`.

Return the maximum average value as a `double`.

##### 🔹 Constraints:
- `1 <= k <= n <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

##### 🧠 Approach: Sliding Window (Fixed Size)

Since we're asked to compute something over **every subarray of a fixed size `k`**, this is a classic case for the **fixed-size sliding window** technique.

##### ✅ Key Idea:
1. Start by computing the sum of the first `k` elements (this forms the first window).
2. Then **slide the window** by one element at a time:
   - Subtract the element that's going out of the window (on the left).
   - Add the new element that's coming into the window (on the right).
3. Keep track of the **maximum sum** observed.
4. Finally, return the maximum sum divided by `k` to get the average.

##### ⏱ Time Complexity:
- **O(n)** — We traverse the array only once.

##### 🔍 Java Code

```java
public class Solution {
    public double findMaxAverage(int[] nums, int k) {
        int windowSum = 0;

        // Step 1: Sum of the first window
        for (int i = 0; i < k; i++) {
            windowSum += nums[i];
        }

        int maxSum = windowSum;

        // Step 2: Slide the window through the array
        for (int i = k; i < nums.length; i++) {
            windowSum += nums[i] - nums[i - k]; // Slide window
            maxSum = Math.max(maxSum, windowSum);
        }

        // Step 3: Return max average
        return (double) maxSum / k;
    }
}
```

---

#### 🧩 Pattern 2: Variable-Size Sliding Window


##### 📘 Problem: Longest Substring Without Repeating Characters (Leetcode 3)

**Link:** [https://leetcode.com/problems/longest-substring-without-repeating-characters/](https://leetcode.com/problems/longest-substring-without-repeating-characters/)

##### 📝 Problem Statement

Given a string `s`, find the length of the **longest substring** without repeating characters.

##### 🔹 Constraints:
- `0 <= s.length <= 5 * 10^4`
- `s` consists of English letters, digits, symbols, and spaces.

##### 🧠 Approach: Sliding Window (Variable Size)

This problem requires us to find the **longest** contiguous substring where no characters repeat — the window **can grow and shrink** depending on whether the constraint (no duplicates) is satisfied.

Hence, this is a perfect case for a **variable-size sliding window**.

##### ✅ Key Idea:

1. Use two pointers: `start` and `end` to denote the current window.
2. Expand the `end` pointer and add characters to a set/map until a **duplicate** is found.
3. If a duplicate is found, shrink the window from the `start` side until the window becomes valid again (no duplicates).
4. Track the **maximum length** of a valid window throughout the process.

---

##### ⏱ Time Complexity:
- **O(n)** — Each character is visited at most twice (once by `end`, once by `start`).
- **O(k)** space — For the character set/map, where `k` is the number of unique characters.

---

##### 🔍 Java Code

```java
import java.util.HashSet;

public class Solution {
    public int lengthOfLongestSubstring(String s) {
        int start = 0, end = 0, maxLen = 0;
        HashSet<Character> seen = new HashSet<>();

        while (end < s.length()) {
            char current = s.charAt(end);

            // If duplicate found, shrink from the left
            while (seen.contains(current)) {
                seen.remove(s.charAt(start));
                start++;
            }

            seen.add(current);
            maxLen = Math.max(maxLen, end - start + 1);
            end++;
        }

        return maxLen;
    }
}
```

---

#### 🧩 Pattern 3: Variable-Size Sliding Window with HashMap/HashSet (Frequency Tracking)

##### 📘 Problem: Minimum Window Substring (Leetcode 76)

**Link:** [https://leetcode.com/problems/minimum-window-substring/](https://leetcode.com/problems/minimum-window-substring/)

##### 📝 Problem Statement

Given two strings `s` and `t`, return the **minimum window substring** of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such substring, return an empty string `""`.

##### 🧠 Approach: Sliding Window with Frequency Map

This problem requires us to find the **smallest substring** in `s` containing all characters of `t`. The window size is **variable** and the condition depends on character **frequencies**.

Hence, we use:
- Two pointers defining the sliding window (`start` and `end`).
- A frequency map to track counts of characters needed.
- A count of how many required characters are currently matched.


##### ✅ Key Idea:

1. Build a frequency map of characters in `t`.
2. Expand the `end` pointer to include characters from `s` and update the map.
3. Once the current window satisfies the requirement (contains all chars of `t`), try shrinking it from `start` to find a smaller valid window.
4. Track the minimum length window that satisfies the condition.

##### ⏱ Time Complexity:
- **O(n + m)** where `n` is the length of `s` and `m` is the length of `t`.
- Each character is visited at most twice by the sliding window pointers.

##### 🔍 Java Code

```java
import java.util.HashMap;

public class Solution {
    public String minWindow(String s, String t) {
        if (s.length() < t.length()) return "";

        // Frequency map for characters in t
        HashMap<Character, Integer> freqMap = new HashMap<>();
        for (char c : t.toCharArray()) {
            freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
        }

        int required = freqMap.size(); // Number of unique chars needed
        int formed = 0;                // How many unique chars currently satisfied

        // Window pointers and result tracking
        int left = 0, right = 0;
        int minLen = Integer.MAX_VALUE;
        int minStart = 0;

        // Map to count chars in the current window
        HashMap<Character, Integer> windowCounts = new HashMap<>();

        while (right < s.length()) {
            char c = s.charAt(right);
            windowCounts.put(c, windowCounts.getOrDefault(c, 0) + 1);

            // Check if current char meets the desired frequency
            if (freqMap.containsKey(c) && windowCounts.get(c).intValue() == freqMap.get(c).intValue()) {
                formed++;
            }

            // Try to contract the window while it still satisfies the condition
            while (left <= right && formed == required) {
                c = s.charAt(left);

                // Update minimum window if smaller
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    minStart = left;
                }

                // Remove left char from window
                windowCounts.put(c, windowCounts.get(c) - 1);
                if (freqMap.containsKey(c) && windowCounts.get(c).intValue() < freqMap.get(c).intValue()) {
                    formed--;
                }

                left++;
            }

            right++;
        }

        return minLen == Integer.MAX_VALUE ? "" : s.substring(minStart, minStart + minLen);
    }
}
```

---