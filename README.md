# 求数组的最大连续子数组和

## LeetCode 53. Maximum Subarray

> Given an integer array nums, find the subarray which has the largest sum and return its sum.

```
Example 1:

Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.


Example 2:

Input: nums = [1]
Output: 1


Example 3:

Input: nums = [5,4,-1,7,8]
Output: 23
```

### 解法

> 采用动态规划，时间复杂度O(N)，空间复杂度O(1)，具体如下：

1. 状态定义

状态f[j]，表示以第j个元素结尾的最大连续子序列和

2. 状态转移方程

f[j] = max{f[j−1] + S[j], S[j]}，其中1≤ j ≤n

情况一：S[j]不独立，与前面的某些数组成一个连续子序列，则最大连续子序列和为f[j−1] + S[j]

情况二：S[j]独立划分成为一段，即连续子序列仅包含一个数S[j]，则最大连续子序列和为S[j]

3. 求解结果
max{f[j]}, 其中1≤j≤n

#### 代码

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        dp = nums.copy()
        res = nums[0]
        for i in range(1, len(nums)):
            dp[i] = max(dp[i-1] + nums[i], dp[i])
            res = max(res, dp[i])
        return res
```

### 变形

> 如何求一个数组两个连续子数组和的最大值，要求两个子数组不能有交集？

```
Example 1:

Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 8
Explanation: [4] has the largest sum = 4; [4] has the largest sum = 4.

Example 2:

Input: nums = [5,4,-1,7,8]
Explanation: [5,4] has the largest sum = 9; [7,8] has the largest sum = 15.
Output: 24
```

#### 解法

- 暴力解法

> 遍历数组的每个下标，将数组分成两部分，每个部分各自求算最大子数组的和，然后求和，最终全局输出最大值。

- 动态规划

> 1. 按照求算最大子数组的和的方法，将数组正序求算一次获得dp1，再逆序求算一次获得dp2

> 2. 这样可以得到数组每个值自左向右和自右向左结尾的最大连续子序列和

> 3. 全局遍历，获取每个值自左向右的最大连续子序列和 + 每个值右侧所有数字自右向左结尾的最大连续子序列和的最大值，即可获取全局最大值。


```
举例来看：
nums = [5,4,-1,7,8]

正序求最大子数组的和如下：
dp1 = [5, 9, 8, 15, 23]

数组逆序，然后求最大子数组的和如下：
nums = [8, 7,-1, 4, 5]
dp2 = [8, 15, 14, 18, 23]

此时dp1[0]代表以5结尾的最大连续子序列和(原数组自左向右)

dp2[4]代表以5结尾的最大连续子序列和(原数组自右向左)

当左侧选5作为最大子序列时，右侧只能以9, 8, 15, 23作为最大子序列，即：dp1[0] + dp2[i])，i=0,1,2,3，因为子数组不能有交集，所以dp2不能包含5
```

##### 代码

```python3
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        n = len(nums)
        # 正序dp
        dp1 = [0] * n
        dp1[0] = nums[0]
        for i in range(1, n):
            dp1[i] = max(dp1[i-1] + nums[i], nums[i])
        
        # 逆序dp
        temp = nums[::-1].copy()
        dp2 = [0] * n
        dp2[0] = temp[0]
        for i in range(1, n):
            dp2[i] = max(dp2[i-1] + temp[i], temp[i])
        
        # 再逆序搞成与原数组对应位置
        dp2 = dp2[::-1]

        res = float('-inf')

        # 遍历每个分界点，右半段数组取最大值
        for i in range(n):
            for j in range(i+1, n):
                res = max(res, dp1[i] + dp2[j])

        return res
```