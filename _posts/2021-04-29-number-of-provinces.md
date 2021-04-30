---
layout: post 
title:  "leetcode - number-of-provinces"
date:   2021/04/29 
excerpt: "number-of-provinces : BFS"
comments: true 
tag:
- algorithm
- LeetCode
---

There are n cities. Some of them are connected, while some are not. If city a is connected directly with city b, and city b is connected directly with city c, then city a is connected indirectly with city c.

A province is a group of directly or indirectly connected cities and no other cities outside of the group.

You are given an n x n matrix isConnected where isConnected[i][j] = 1 if the ith city and the jth city are directly connected, and isConnected[i][j] = 0 otherwise.

Return the total number of provinces.

## Example 1:
~~~
Input: isConnected = [[1,1,0],[1,1,0],[0,0,1]]
Output: 2
~~~
## Example 2:
~~~
Input: isConnected = [[1,0,0],[0,1,0],[0,0,1]]
Output: 3
~~~

{% highlight java %}
package leetcode;

import java.util.LinkedList;
import java.util.Queue;

/**
 * Created by mkhwang on 2021/04/28
 * Email : orange2652@gmail.com
 * Github : https://github.com/myeongkwonhwang
 */
public class NumberOfProvinces {

    int answer;

    public int findCircleNum(int[][] isConnected) {

        boolean[] visited = new boolean[isConnected.length];

        for(int i=0; i< isConnected.length; i++){
            bfs(i, visited, isConnected);
        }
        return answer;
    }

    public void bfs(int i, boolean[] visited, int[][] isConnected) {
        if(visited[i]) return;
        answer++;
        Queue<Integer> queue = new LinkedList<>();
        queue.add(i);
        while (!queue.isEmpty()) {
            int curr = queue.poll();
            visited[curr] = true;
            for (int j = 0; j < isConnected[curr].length; j++) {
                if(isConnected[curr][j] != 0 && !visited[j]) {
                    queue.add(j);
                }
            }
        }
    }

}
{% endhighlight %} 
