---
layout: post 
title:  "leetcode - maximum-subarray"
date:   2021/05/20
excerpt: "maximum-subarray"
comments: true 
tag:
- algorithm
- LeetCode
---

Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.



## Example 1:
~~~
Input: nums = [-2,1,-3,4,-1,2,1,-5,4]
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
~~~

## Example 2:
~~~
Input: nums = [1]
Output: 1
~~~

## Example 3:
~~~
Input: nums = [5,4,-1,7,8]
Output: 23
~~~

## Constraints:
- 1 <= nums.length <= 3 * 104
- -105 <= nums[i] <= 105 


Follow up: If you have figured out the O(n) solution, try coding another solution using the divide and conquer approach, which is more subtle.


{% highlight java %}
package leetcode;

/**
 * Created by mkhwang on 2021/05/20
 * Email : orange2652@gmail.com
 * Github : https://github.com/myeongkwonhwang
 */
public class MaximumSubarray {

    public static void main(String[] args) {
        int[] nums = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
        System.out.println(maxSubArray(nums));
    }
    public static int maxSubArray(int[] nums) {
        int currMax = nums[0];
        int max = nums[0];

        for (int i = 1; i < nums.length; i++) {
            currMax = Math.max(nums[i], nums[i] + currMax);
            max = Math.max(currMax, max);
        }
        return max;
    }
}

{% endhighlight %} 
