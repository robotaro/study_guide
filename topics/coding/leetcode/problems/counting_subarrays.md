
This is a very good explanation fron Adytia:
https://leetcode.com/problems/continuous-subarray-sum/solutions/5276981/prefix-sum-hashmap-patterns-7-problems/?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days


## Prefix Sum + Hashmap Approach

We can apply this approach in problems related to Subarrays, problems where we are asked to

1. Count the subarrays with some condition
2. Find Maximum Length Subarray with some condition
3. Check if subarray exists with some given condition

Mainly the problems dealing with Subarray sum .

_Approach works on the following principle_

> If the prefix sum up to `ith` index is `X`, and the  
> `prefix sum` up to `jth` index is `Y` and  
> it is found that `Y = X +k`, then the required subarray is found with `i` as start index and `j` as end index.

> To store the index value and the sum of elements up to that index a hashmap can be used.

#### _Why we need this approach of Hashmap +Prefix Sum ?_

So basically for subarray related problems Brute Force method takes O(n^2) time to process each subarray + extra time in processing .  
But with this approach only O(n) time is taken .  
Now you might wonder that what is stopping you from using Sliding window approach for such problems

> Sliding window is only applicable when we know for sure if the prefixsum is an increasing or decreasing function(i.e. Monotonous in nature)

So for problems where negative input is given this approach of PrefixSum + Hashmap is the best way to solve such problems.

---

### 1. Count Subarrays with some given condtion

Data Structures needed :

- **Integer Variable** for cumulative sum
- **Unordered Map**
- Map **Initialisation** : **mp[0]=1**

Lets solve 3 problems using Prefix Sum + Hashmap pattern

### [✏️Problem 1.1 --> 560. Count Subarray sum equals k](https://leetcode.com/problems/subarray-sum-equals-k/)

> **Condition : Sum of subarray equals k**  
> Negative values **ALLOWED** in Input

Code

---

### [✏️Problem 1.2 --> 974. Count Subarray Sums Divisible by K](https://leetcode.com/problems/subarray-sums-divisible-by-k/)

> **Condition** : Subarrays that have a sum divisible by k.  
> Negative values **ALLOWED** in Input

Code

---

### [✏️Problem 1.3--> 930. Count Binary Subarrays with given SUM](https://leetcode.com/problems/binary-subarrays-with-sum/)

> **Condition** : (Binary)Subarrays with given Sum

> **Negative values are not allowed in input. Because of this reason, we can also solve this problem using Sliding window too.**

> **Sliding window is only applicable when we know for sure if the prefixsum is an increasing or decreasing function(i.e. Monotonous in nature)**

Code

---

## 2. Maximum length Subarray with given condition

Data Structures needed :

- **Integer Variable** for cumulative sum
- **Unordered Map**
- Map **Initialisation** : **mp[0]=-1** `Here we are dealing with index that's why we initalize it to -1`

### [✏️Problem 2.1 --> 525.Contiguous Array](https://leetcode.com/problems/contiguous-array/)

> **Condition** : Binary Subarray with an equal number of 0 and 1

Code

---

### ✏️Problem 2.2 --> 325.Maximum Size Subarray Sum Equals k (Premium)]

_**Problem is Available on other Platforms.**_

> Problem Statement : Given an array nums and a target value k, find the maximum length of a subarray that sums to k. If there isn't one, return 0 instead.

> Condition: subarray that sums to k

> Negative values **ALLOWED** in Input

Code

---

## [✏️Problem 2.3 -->1658. Minimum Operations to Reduce X to Zero](https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/)

> **Problem Reduced to Above problem of Maximum size subarray sum**

Code

---

## 3. Check if a subarray exists with given condition

### [✏️Problem 3.1 --> 523. Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/)

> Given an integer array nums and an integer k, return true if nums has a good subarray or false otherwise.

> Conditions: Length of subarray is at least two  
> The sum of the elements of the subarray is a multiple of k

> **Why sliding window not applicable here despite positive input?**  
> _We need to check if the sum of the subarray is a multiple of k, which adds complexity to he problem. We can't determine this condition by simply adjusting the window size. if instead of `modulo` the `sum` was asked to be compared then we can form a logic to decrement or increment window size_

Code

[

Previous

Continuous Subarray Sum



](https://leetcode.com/problems/continuous-subarray-sum/solutions/5259908/continuous-subarray-sum/?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

Next

Java O(n) time O(k) space



](https://leetcode.com/problems/continuous-subarray-sum/solutions/99499/java-o-n-time-o-k-space/?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)

Comments (41)

Sort by:Best

PreviewComment

[![](https://assets.leetcode.com/users/avatars/avatar_1650588391.png)](https://leetcode.com/u/jiayujerryLiu/)

[jLiucoder](https://leetcode.com/u/jiayujerryLiu/)

![Data Structure I](https://assets.leetcode.com/static_assets/others/DS_I.png)

Jun 08, 2024

bro nice job, was looking for this pattern, getting stuck on these like crazy

35

Reply

Share

[![](https://assets.leetcode.com/users/avatars/avatar_1671906368.png)](https://leetcode.com/u/selvaarumugamn/)

[selvaarumugamn](https://leetcode.com/u/selvaarumugamn/)

![50 Days Badge 2023](https://assets.leetcode.com/static_assets/marketing/lg50.png)

Jun 08, 2024

This is the best editorial I have ever came across on leetcode

9

Reply

[![](https://assets.leetcode.com/users/gauravsahni25/avatar_1713805767.png)](https://leetcode.com/u/gauravsahni25/)

[gauravsahni25](https://leetcode.com/u/gauravsahni25/)

![50 Days Badge 2024](https://assets.leetcode.com/static_assets/marketing/2024-50-lg.png)

Aug 25, 2024

[@Aditya](https://leetcode.com/u/mercer80) Dude leetcode should hire you!  
Simple, effective, and to the point!  
I have lost count of how many times Leetcode editorial authors try to sound smart while giving convoluted answers and code.  
Some authors need to understand that people pay to understand the problem and solution, not to see how smart they are!

you're a true hero my dude! Keep it up!

4

Reply

[![](https://assets.leetcode.com/users/default_avatar.jpg)](https://leetcode.com/u/Kartik-mittal/)

[Kartik-mittal](https://leetcode.com/u/Kartik-mittal/)

![365 Days Badge](https://assets.leetcode.com/static_assets/marketing/lg365.png)

Jun 20, 2024

Amazing solution, not only teaches us this specific question but the complete pattern, my best 40 minutes spent on leetcode so far, share the other pattern explanations if you have any,

3

Reply

[![](https://assets.leetcode.com/users/fake_priyanshu/avatar_1724559802.png)](https://leetcode.com/u/priyanshu_0g/)

[Priyanshu Goyal](https://leetcode.com/u/priyanshu_0g/)

Jun 09, 2024

can you plz explain, why we initialize mpp[0] with 1 and -1?

3

Reply

[![](https://assets.leetcode.com/users/maria_q/avatar_1719224377.png)](https://leetcode.com/u/maria_q/)

[Maria](https://leetcode.com/u/maria_q/)

![200 Days Badge 2024](https://assets.leetcode.com/static_assets/marketing/2024-200-lg.png)

Jun 08, 2024

There is [one line solution](https://leetcode.com/problems/continuous-subarray-sum/solutions/5278604/one-line-solution/) for this task - may be interesting!

3

Show 1 Replies

Reply

[![](https://assets.leetcode.com/users/default_avatar.jpg)](https://leetcode.com/u/Libran10/)

[Libran10](https://leetcode.com/u/Libran10/)

Oct 29, 2024

Legend

1

Reply

[![](https://assets.leetcode.com/users/default_avatar.jpg)](https://leetcode.com/u/krishna_0n/)

[Chandan](https://leetcode.com/u/krishna_0n/)

Oct 11, 2024

Dude this is the great editorial, I have ever seen on Leetcode!!!

1

Reply

[![](https://assets.leetcode.com/users/mb36/avatar_1726409133.png)](https://leetcode.com/u/Bhawna_Raheja/)

[bhawna](https://leetcode.com/u/Bhawna_Raheja/)

![50 Days Badge 2023](https://assets.leetcode.com/static_assets/marketing/lg50.png)

Sep 20, 2024

such a great content

1

Reply

[![](https://assets.leetcode.com/users/Namanjr_333/avatar_1725414981.png)](https://leetcode.com/u/Namanjr_333/)

[Namanjr333](https://leetcode.com/u/Namanjr_333/)

![Nov LeetCoding Challenge](https://leetcode.com/static/images/badges/dcc-2024-11.png)

Aug 06, 2024

upvoted

1

Reply

12345

324

41

Continuous Subarray Sum - LeetCode

[

![](https://assets.leetcode.com/users/images/5e0612cd-020b-49ac-893b-e4b3e7315f01_1720089938.3952677.png)Meta

](https://leetcode.com/company/facebook/?favoriteSlug=facebook-thirty-days)

[

543. Diameter of Binary Tree

Easy





](https://leetcode.com/problems/diameter-of-binary-tree?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

71. Simplify Path

Med.





](https://leetcode.com/problems/simplify-path?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

88. Merge Sorted Array

Easy





](https://leetcode.com/problems/merge-sorted-array?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

125. Valid Palindrome

Easy





](https://leetcode.com/problems/valid-palindrome?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

146. LRU Cache

Med.





](https://leetcode.com/problems/lru-cache?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

670. Maximum Swap

Med.





](https://leetcode.com/problems/maximum-swap?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

162. Find Peak Element

Med.





](https://leetcode.com/problems/find-peak-element?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

680. Valid Palindrome II

Easy





](https://leetcode.com/problems/valid-palindrome-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1570. Dot Product of Two Sparse Vectors

Med.





](https://leetcode.com/problems/dot-product-of-two-sparse-vectors?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

215. Kth Largest Element in an Array

Med.





](https://leetcode.com/problems/kth-largest-element-in-an-array?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

236. Lowest Common Ancestor of a Binary Tree

Med.





](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1650. Lowest Common Ancestor of a Binary Tree III

Med.





](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree-iii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

314. Binary Tree Vertical Order Traversal

Med.





](https://leetcode.com/problems/binary-tree-vertical-order-traversal?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1249. Minimum Remove to Make Valid Parentheses

Med.





](https://leetcode.com/problems/minimum-remove-to-make-valid-parentheses?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

408. Valid Word Abbreviation

Easy





](https://leetcode.com/problems/valid-word-abbreviation?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

938. Range Sum of BST

Easy





](https://leetcode.com/problems/range-sum-of-bst?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

973. K Closest Points to Origin

Med.





](https://leetcode.com/problems/k-closest-points-to-origin?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

523. Continuous Subarray Sum

Med.





](https://leetcode.com/problems/continuous-subarray-sum?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

50. Pow(x, n)

Med.





](https://leetcode.com/problems/powx-n?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

56. Merge Intervals

Med.





](https://leetcode.com/problems/merge-intervals?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

34. Find First and Last Position of Element in Sorted Array

Med.





](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

199. Binary Tree Right Side View

Med.





](https://leetcode.com/problems/binary-tree-right-side-view?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1091. Shortest Path in Binary Matrix

Med.





](https://leetcode.com/problems/shortest-path-in-binary-matrix?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

227. Basic Calculator II

Med.





](https://leetcode.com/problems/basic-calculator-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

560. Subarray Sum Equals K

Med.





](https://leetcode.com/problems/subarray-sum-equals-k?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

339. Nested List Weight Sum

Med.





](https://leetcode.com/problems/nested-list-weight-sum?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

347. Top K Frequent Elements

Med.





](https://leetcode.com/problems/top-k-frequent-elements?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

791. Custom Sort String

Med.





](https://leetcode.com/problems/custom-sort-string?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

498. Diagonal Traverse

Med.





](https://leetcode.com/problems/diagonal-traverse?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

138. Copy List with Random Pointer

Med.





](https://leetcode.com/problems/copy-list-with-random-pointer?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

987. Vertical Order Traversal of a Binary Tree

Hard





](https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

528. Random Pick with Weight

Med.





](https://leetcode.com/problems/random-pick-with-weight?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

31. Next Permutation

Med.





](https://leetcode.com/problems/next-permutation?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

766. Toeplitz Matrix

Easy





](https://leetcode.com/problems/toeplitz-matrix?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1762. Buildings With an Ocean View

Med.





](https://leetcode.com/problems/buildings-with-an-ocean-view?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1539. Kth Missing Positive Number

Easy





](https://leetcode.com/problems/kth-missing-positive-number?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

346. Moving Average from Data Stream

Easy





](https://leetcode.com/problems/moving-average-from-data-stream?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

921. Minimum Add to Make Parentheses Valid

Med.





](https://leetcode.com/problems/minimum-add-to-make-parentheses-valid?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

23. Merge k Sorted Lists

Hard





](https://leetcode.com/problems/merge-k-sorted-lists?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

398. Random Pick Index

Med.





](https://leetcode.com/problems/random-pick-index?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

827. Making A Large Island

Hard





](https://leetcode.com/problems/making-a-large-island?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

249. Group Shifted Strings

Med.





](https://leetcode.com/problems/group-shifted-strings?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

207. Course Schedule

Med.





](https://leetcode.com/problems/course-schedule?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

721. Accounts Merge

Med.





](https://leetcode.com/problems/accounts-merge?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

121. Best Time to Buy and Sell Stock

Easy





](https://leetcode.com/problems/best-time-to-buy-and-sell-stock?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

426. Convert Binary Search Tree to Sorted Doubly Linked List

Med.





](https://leetcode.com/problems/convert-binary-search-tree-to-sorted-doubly-linked-list?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

708. Insert into a Sorted Circular Linked List

Med.





](https://leetcode.com/problems/insert-into-a-sorted-circular-linked-list?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

129. Sum Root to Leaf Numbers

Med.





](https://leetcode.com/problems/sum-root-to-leaf-numbers?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1004. Max Consecutive Ones III

Med.





](https://leetcode.com/problems/max-consecutive-ones-iii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

986. Interval List Intersections

Med.





](https://leetcode.com/problems/interval-list-intersections?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

270. Closest Binary Search Tree Value

Easy





](https://leetcode.com/problems/closest-binary-search-tree-value?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

14. Longest Common Prefix

Easy





](https://leetcode.com/problems/longest-common-prefix?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

163. Missing Ranges

Easy





](https://leetcode.com/problems/missing-ranges?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

415. Add Strings

Easy





](https://leetcode.com/problems/add-strings?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

76. Minimum Window Substring

Hard





](https://leetcode.com/problems/minimum-window-substring?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1216. Valid Palindrome III

Hard





](https://leetcode.com/problems/valid-palindrome-iii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1. Two Sum

Easy





](https://leetcode.com/problems/two-sum?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

282. Expression Add Operators

Hard





](https://leetcode.com/problems/expression-add-operators?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

133. Clone Graph

Med.





](https://leetcode.com/problems/clone-graph?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1768. Merge Strings Alternately

Easy





](https://leetcode.com/problems/merge-strings-alternately?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

65. Valid Number

Hard





](https://leetcode.com/problems/valid-number?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2. Add Two Numbers

Med.





](https://leetcode.com/problems/add-two-numbers?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

19. Remove Nth Node From End of List

Med.





](https://leetcode.com/problems/remove-nth-node-from-end-of-list?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

127. Word Ladder

Hard





](https://leetcode.com/problems/word-ladder?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

863. All Nodes Distance K in Binary Tree

Med.





](https://leetcode.com/problems/all-nodes-distance-k-in-binary-tree?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

958. Check Completeness of a Binary Tree

Med.





](https://leetcode.com/problems/check-completeness-of-a-binary-tree?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

647. Palindromic Substrings

Med.





](https://leetcode.com/problems/palindromic-substrings?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2667. Create Hello World Function

Easy





](https://leetcode.com/problems/create-hello-world-function?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

169. Majority Element

Easy





](https://leetcode.com/problems/majority-element?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1757. Recyclable and Low Fat Products

Easy





](https://leetcode.com/problems/recyclable-and-low-fat-products?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

20. Valid Parentheses

Easy





](https://leetcode.com/problems/valid-parentheses?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

3. Longest Substring Without Repeating Characters

Med.





](https://leetcode.com/problems/longest-substring-without-repeating-characters?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

78. Subsets

Med.





](https://leetcode.com/problems/subsets?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

5. Longest Palindromic Substring

Med.





](https://leetcode.com/problems/longest-palindromic-substring?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

139. Word Break

Med.





](https://leetcode.com/problems/word-break?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

15. 3Sum

Med.





](https://leetcode.com/problems/3sum?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

643. Maximum Average Subarray I

Easy





](https://leetcode.com/problems/maximum-average-subarray-i?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

26. Remove Duplicates from Sorted Array

Easy





](https://leetcode.com/problems/remove-duplicates-from-sorted-array?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

9. Palindrome Number

Easy





](https://leetcode.com/problems/palindrome-number?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

13. Roman to Integer

Easy





](https://leetcode.com/problems/roman-to-integer?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

695. Max Area of Island

Med.





](https://leetcode.com/problems/max-area-of-island?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

7. Reverse Integer

Med.





](https://leetcode.com/problems/reverse-integer?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

42. Trapping Rain Water

Hard





](https://leetcode.com/problems/trapping-rain-water?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

219. Contains Duplicate II

Easy





](https://leetcode.com/problems/contains-duplicate-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

286. Walls and Gates

Med.





](https://leetcode.com/problems/walls-and-gates?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1047. Remove All Adjacent Duplicates In String

Easy





](https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

53. Maximum Subarray

Med.





](https://leetcode.com/problems/maximum-subarray?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

636. Exclusive Time of Functions

Med.





](https://leetcode.com/problems/exclusive-time-of-functions?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

81. Search in Rotated Sorted Array II

Med.





](https://leetcode.com/problems/search-in-rotated-sorted-array-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

67. Add Binary

Easy





](https://leetcode.com/problems/add-binary?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

62. Unique Paths

Med.





](https://leetcode.com/problems/unique-paths?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1422. Maximum Score After Splitting a String

Easy





](https://leetcode.com/problems/maximum-score-after-splitting-a-string?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

54. Spiral Matrix

Med.





](https://leetcode.com/problems/spiral-matrix?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2270. Number of Ways to Split Array

Med.





](https://leetcode.com/problems/number-of-ways-to-split-array?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1930. Unique Length-3 Palindromic Subsequences

Med.





](https://leetcode.com/problems/unique-length-3-palindromic-subsequences?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2381. Shifting Letters II

Med.





](https://leetcode.com/problems/shifting-letters-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1769. Minimum Number of Operations to Move All Balls to Each Box

Med.





](https://leetcode.com/problems/minimum-number-of-operations-to-move-all-balls-to-each-box?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

394. Decode String

Med.





](https://leetcode.com/problems/decode-string?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

79. Word Search

Med.





](https://leetcode.com/problems/word-search?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

824. Goat Latin

Easy





](https://leetcode.com/problems/goat-latin?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

22. Generate Parentheses

Med.





](https://leetcode.com/problems/generate-parentheses?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

536. Construct Binary Tree from String

Med.





](https://leetcode.com/problems/construct-binary-tree-from-string?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1011. Capacity To Ship Packages Within D Days

Med.





](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2185. Counting Words With a Given Prefix

Easy





](https://leetcode.com/problems/counting-words-with-a-given-prefix?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

489. Robot Room Cleaner

Hard





](https://leetcode.com/problems/robot-room-cleaner?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

4. Median of Two Sorted Arrays

Hard





](https://leetcode.com/problems/median-of-two-sorted-arrays?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

348. Design Tic-Tac-Toe

Med.





](https://leetcode.com/problems/design-tic-tac-toe?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1400. Construct K Palindrome Strings

Med.





](https://leetcode.com/problems/construct-k-palindrome-strings?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

8. String to Integer (atoi)

Med.





](https://leetcode.com/problems/string-to-integer-atoi?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

155. Min Stack

Med.





](https://leetcode.com/problems/min-stack?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2116. Check if a Parentheses String Can Be Valid

Med.





](https://leetcode.com/problems/check-if-a-parentheses-string-can-be-valid?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

75. Sort Colors

Med.





](https://leetcode.com/problems/sort-colors?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

21. Merge Two Sorted Lists

Easy





](https://leetcode.com/problems/merge-two-sorted-lists?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

128. Longest Consecutive Sequence

Med.





](https://leetcode.com/problems/longest-consecutive-sequence?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1378. Replace Employee ID With The Unique Identifier

Easy





](https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

772. Basic Calculator III

Hard





](https://leetcode.com/problems/basic-calculator-iii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

530. Minimum Absolute Difference in BST

Easy





](https://leetcode.com/problems/minimum-absolute-difference-in-bst?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

242. Valid Anagram

Easy





](https://leetcode.com/problems/valid-anagram?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

38. Count and Say

Med.





](https://leetcode.com/problems/count-and-say?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2013. Detect Squares

Med.





](https://leetcode.com/problems/detect-squares?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

287. Find the Duplicate Number

Med.





](https://leetcode.com/problems/find-the-duplicate-number?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

149. Max Points on a Line

Hard





](https://leetcode.com/problems/max-points-on-a-line?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

167. Two Sum II - Input Array Is Sorted

Med.





](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

40. Combination Sum II

Med.





](https://leetcode.com/problems/combination-sum-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

200. Number of Islands

Med.





](https://leetcode.com/problems/number-of-islands?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

424. Longest Repeating Character Replacement

Med.





](https://leetcode.com/problems/longest-repeating-character-replacement?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

238. Product of Array Except Self

Med.





](https://leetcode.com/problems/product-of-array-except-self?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

49. Group Anagrams

Med.





](https://leetcode.com/problems/group-anagrams?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1891. Cutting Ribbons

Med.





](https://leetcode.com/problems/cutting-ribbons?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1748. Sum of Unique Elements

Easy





](https://leetcode.com/problems/sum-of-unique-elements?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

253. Meeting Rooms II

Med.





](https://leetcode.com/problems/meeting-rooms-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1280. Students and Examinations

Easy





](https://leetcode.com/problems/students-and-examinations?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1368. Minimum Cost to Make at Least One Valid Path in a Grid

Hard





](https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

239. Sliding Window Maximum

Hard





](https://leetcode.com/problems/sliding-window-maximum?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

317. Shortest Distance from All Buildings

Hard





](https://leetcode.com/problems/shortest-distance-from-all-buildings?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

118. Pascal's Triangle

Easy





](https://leetcode.com/problems/pascals-triangle?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

3428. Maximum and Minimum Sums of at Most Size K Subsequences

Med.





](https://leetcode.com/problems/maximum-and-minimum-sums-of-at-most-size-k-subsequences?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1424. Diagonal Traverse II

Med.





](https://leetcode.com/problems/diagonal-traverse-ii?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

70. Climbing Stairs

Easy





](https://leetcode.com/problems/climbing-stairs?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

393. UTF-8 Validation

Med.





](https://leetcode.com/problems/utf-8-validation?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

875. Koko Eating Bananas

Med.





](https://leetcode.com/problems/koko-eating-bananas?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

1493. Longest Subarray of 1's After Deleting One Element

Med.





](https://leetcode.com/problems/longest-subarray-of-1s-after-deleting-one-element?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

84. Largest Rectangle in Histogram

Hard





](https://leetcode.com/problems/largest-rectangle-in-histogram?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

380. Insert Delete GetRandom O(1)

Med.





](https://leetcode.com/problems/insert-delete-getrandom-o1?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

771. Jewels and Stones

Easy





](https://leetcode.com/problems/jewels-and-stones?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

934. Shortest Bridge

Med.





](https://leetcode.com/problems/shortest-bridge?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

12. Integer to Roman

Med.





](https://leetcode.com/problems/integer-to-roman?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

66. Plus One

Easy





](https://leetcode.com/problems/plus-one?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

2326. Spiral Matrix IV

Med.





](https://leetcode.com/problems/spiral-matrix-iv?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

39. Combination Sum

Med.





](https://leetcode.com/problems/combination-sum?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

173. Binary Search Tree Iterator

Med.





](https://leetcode.com/problems/binary-search-tree-iterator?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

485. Max Consecutive Ones

Easy





](https://leetcode.com/problems/max-consecutive-ones?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

301. Remove Invalid Parentheses

Hard





](https://leetcode.com/problems/remove-invalid-parentheses?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

739. Daily Temperatures

Med.





](https://leetcode.com/problems/daily-temperatures?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

28. Find the Index of the First Occurrence in a String

Easy





](https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)[

268. Missing Number

Easy





](https://leetcode.com/problems/missing-number?envType=company&envId=facebook&favoriteSlug=facebook-thirty-days)