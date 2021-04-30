---
layout: post 
title:  "leetcode - rotting-oranges"
date:   2021/04/29 
excerpt: "rotting-oranges : BFS"
comments: true 
tag:
- algorithm
- LeetCode
---

You are given an m x n grid where each cell can have one of three values:

0 representing an empty cell,
1 representing a fresh orange, or
2 representing a rotten orange.
Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.

## Example 1:
~~~
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4
~~~

## Example 2:
~~~
Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.
~~~

## Example 3:
~~~
Input: grid = [[0,2]]
Output: 0
Explanation: Since there are already no fresh oranges at minute 0, the answer is just 0.
~~~
{% highlight java %}
package leetcode;

import java.util.LinkedList;
import java.util.Queue;

/**
 * Created by mkhwang on 2021/04/29
 * Email : orange2652@gmail.com
 * Github : https://github.com/myeongkwonhwang
 */
public class RottingOranges {

    int dx[] = {1,0,-1,0};
    int dy[] = {0,1,0,-1};
    int visited[][];
    int x;
    int y;

    public int orangesRotting(int[][] grid) {
        int result = 0;
        x = grid.length;
        y = grid[0].length;
        visited = new int[x][y];
        Queue<Node> q = new LinkedList<>();

        for (int i = 0; i < x; i++) {
            for (int j = 0; j < y; j++) {
                visited[i][j] = -1;
                if(grid[i][j] == 2){
                    q.add(new Node(i,j));
                    visited[i][j] = 0;
                }
            }
        }
        bfs(q, grid);

        for (int i = 0; i < x; i++) {
            for (int j = 0; j < y; j++) {
                if(visited[i][j] == -1 && grid[i][j] == 1) return -1;
                result = Math.max(result, visited[i][j]);
            }
        }

        return result;
    }

    private void bfs(Queue<Node> queue, int[][] grid) {
        while (!queue.isEmpty()) {
            Node curr = queue.poll();
            for (int dir = 0; dir < 4; dir++) {
                int nx = curr.x + dx[dir];
                int ny = curr.y + dy[dir];

                if(nx < 0 || nx >= x || ny < 0 || ny >= y) continue;
                if(visited[nx][ny] > -1 || grid[nx][ny] == 0) continue;
                visited[nx][ny] = visited[curr.x][curr.y]+1;
                queue.add(new Node(nx, ny));
            }
        }
    }

    public class Node {
        private int x;
        private int y;

        public Node (int x, int y){
            this.x = x;
            this.y = y;
        }
    }
}
{% endhighlight %} 
