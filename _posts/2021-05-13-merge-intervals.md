---
layout: post 
title:  "leetcode - merge-intervals"
date:   2021/05/13 
excerpt: "merge-intervals : LinkedList"
comments: true 
tag:
- algorithm
- LeetCode
---

Given an array of intervals where intervals[i] = [starti, endi], merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.



## Example 1:
~~~
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
~~~

## Example 2:
~~~
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.
~~~

{% highlight java %}
package leetcode;

import java.util.Arrays;
import java.util.Comparator;
import java.util.LinkedList;

/**
 * Created by mkhwang on 2021/05/13
 * Email : orange2652@gmail.com
 * Github : https://github.com/myeongkwonhwang
 */
public class MergeIntervals {
    static class Solution { 
        public int[][] merge(int[][] intervals) {
            Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
            LinkedList<int[]> merged = new LinkedList<>();
            for (int[] interval : intervals) {
                if (merged.isEmpty() || merged.getLast()[1] < interval[0]) {
                    merged.add(interval);
                } else {
                    merged.getLast()[1] = Math.max(merged.getLast()[1], interval[1]);
                }
            }
            return merged.toArray(new int[merged.size()][]);
        }
    }
}
{% endhighlight %} 
