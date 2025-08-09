---
layout: post
title: "Two Pointers : Patterns & Use Cases"
slug: "two-pointers-technique"
date: 2025-06-16
author: Anubhav Srivastava
tags: [algorithms, two pointers]
version: 1.0
---

* TOC
{:toc}

---

Two Pointer technique uses **two indices** that traverse the data structure (typically arrays or linked lists) in a **coordinated** way. Depending on the problem, the pointers can behave as follows:

* Start from both ends and move toward the center
* Move in the same direction
* Move at different speeds
* Reset based on certain conditions

#### ✅ Why to Use this technique?

* This technique reduces brute-force approaches
* It Works great with sorted arrays or lists
* It Saves space and improves runtime

---

### 💡 Pattern 1: Opposite Ends (Converging Pointers)

#### 📍 Problem: [Leetcode 167 - Two Sum II (Input Array is Sorted)](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

**Given** a sorted array of integers, find two numbers such that they add up to a specific target.

##### 🧠 Intuition:

We Start with one pointer at the beginning and one at the end. Since the array is sorted:

* If the sum is too small, we move the left pointer right
* If the sum is too large, we move the right pointer left

##### ✅ Code:

```java
public int[] twoSum(int[] numbers, int target) {
    int left = 0, right = numbers.length - 1;
    while (left < right) {
        int sum = numbers[left] + numbers[right];
        if (sum == target) return new int[]{left + 1, right + 1};
        else if (sum < target) left++;
        else right--;
    }
    return new int[]{-1, -1};
}
```

##### ⏱️ Time: O(n)    📊 Space: O(1)

---

### 💡 Pattern 2: Same Direction (Fast & Slow Pointers)

#### 📍 Problem: [Leetcode 26 - Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)

**Given** a sorted array, remove duplicates in-place.

##### 🧠 Intuition:

Use two pointers:

* `slow` marks the position to overwrite
* `fast` explores the array

##### ✅ Code:

```java
public int removeDuplicates(int[] nums) {
    int slow = 0;
    for (int fast = 1; fast < nums.length; fast++) {
        if (nums[fast] != nums[slow]) {
            nums[++slow] = nums[fast];
        }
    }
    return slow + 1;
}
```

##### ⏱️ Time: O(n)    📊 Space: O(1)

---

### 💡 Pattern 3: Linked List Tricks (Cycle, Intersection, Middle)

#### 📍 Problem: [Leetcode 160 - Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

**Find** the node at which two singly linked lists intersect.

##### 🧠 Intuition:

We use two pointers that switch heads once they reach the end. They will meet at the intersection or both hit `null`.

> This approach works because by switching the heads when a pointer reaches the end of its list, we ensure that both pointers traverse **the same total number of nodes** — one travels A then B, the other B then A. If the two lists intersect, the pointers will align at the intersection after walking the same combined distance. If they don’t intersect, both will reach `null` at the same time. This guarantees either a meeting point (intersection) or a synchronized end, without needing to know the lengths of the lists in advance.

##### ✅ Code:

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    ListNode a = headA, b = headB;
    while (a != b) {
        a = (a == null) ? headB : a.next;
        b = (b == null) ? headA : b.next;
    }
    return a;
}
```

##### ⏱️ Time: O(m + n)    📊 Space: O(1)

---

### 💡 Pattern 4: One Fixed, One Searching

#### 📍 Problem: [Leetcode 15 - 3Sum](https://leetcode.com/problems/3sum/)

**Find** all unique triplets in the array which gives the sum of zero.

##### 🧠 Intuition:

* Firstly, let's Sort the array
* Then we fix the first number and use two pointers to find the remaining two

##### ✅ Code:

```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> res = new ArrayList<>();
    for (int i = 0; i < nums.length - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue;
        int left = i + 1, right = nums.length - 1;
        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == 0) {
                res.add(Arrays.asList(nums[i], nums[left], nums[right]));
                left++; right--;
                while (left < right && nums[left] == nums[left - 1]) left++;
                while (left < right && nums[right] == nums[right + 1]) right--;
            } else if (sum < 0) left++;
            else right--;
        }
    }
    return res;
}
```

##### ⏱️ Time: O(n^2)    📊 Space: O(1) (excluding output)

---

### 💡 Pattern 5: Bidirectional Scan

#### 📍 Problem: [Leetcode 42 - Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)

**Compute** how much water can be trapped after raining given elevation map.

##### 🧠 Intuition:

We keep two pointers and track the max height seen so far from both ends.

> This approach works because the amount of water that can be trapped at any index depends on the **minimum of the maximum heights** to its left and right. By using two pointers—one starting from the left and one from the right—we can efficiently track the maximum height seen so far from each side (`maxLeft` and `maxRight`). At each step, we move the pointer on the side with the **smaller height**, because the trapped water at that point is limited by that smaller boundary. This ensures that we only need a single pass through the array, without needing to precompute separate left/right max arrays, achieving **O(n) time** and **O(1) space**.

##### ✅ Code:

```java
public int trap(int[] height) {
    int left = 0, right = height.length - 1;
    int maxLeft = 0, maxRight = 0, res = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            if (height[left] >= maxLeft) maxLeft = height[left];
            else res += maxLeft - height[left];
            left++;
        } else {
            if (height[right] >= maxRight) maxRight = height[right];
            else res += maxRight - height[right];
            right--;
        }
    }
    return res;
}
```

##### ⏱️ Time: O(n)    📊 Space: O(1)

---

### 💡 Pattern 6: Merge-Like (Sorted Arrays)

#### 📍 Problem: [Leetcode 88 - Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)

**Merge** two sorted arrays into one in-place.

##### 🧠 Intuition:

We start from the back and fill in the largest values to avoid overwriting.

##### ✅ Code:

```java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int i = m - 1, j = n - 1, k = m + n - 1;
    while (i >= 0 && j >= 0) {
        if (nums1[i] > nums2[j]) nums1[k--] = nums1[i--];
        else nums1[k--] = nums2[j--];
    }
    while (j >= 0) nums1[k--] = nums2[j--];
}
```

##### ⏱️ Time: O(m + n)    📊 Space: O(1)

---

### 💡 Pattern 7: Partition-Based Scanning

#### 📍 Problem: [Leetcode 75 - Sort Colors](https://leetcode.com/problems/sort-colors/)

**Sort** an array with only 0s, 1s, and 2s in-place.

##### 🧠 Intuition:

In this problem, we use three pointers to divide the array into regions (like the Dutch National Flag).

##### ✅ Code:

```java
public void sortColors(int[] nums) {
    int low = 0, mid = 0, high = nums.length - 1;
    while (mid <= high) {
        if (nums[mid] == 0) swap(nums, low++, mid++);
        else if (nums[mid] == 1) mid++;
        else swap(nums, mid, high--);
    }
}

private void swap(int[] nums, int i, int j) {
    int temp = nums[i]; nums[i] = nums[j]; nums[j] = temp;
}
```

##### ⏱️ Time: O(n)    📊 Space: O(1)

---

*The Two Pointer Technique isn't a single trick — it's a **collection of elegant patterns** that appear in countless problems.*