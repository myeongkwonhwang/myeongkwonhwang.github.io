---
layout: post 
title:  "leetcode - minimum-depth-of-binary-tree"
date:   2021/04/21 
excerpt: "two-sum - minimum-depth-of-binary-tree : BFS"
comments: true 
tag:
- algorithm
- LeetCode
---

Given a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

Note: A leaf is a node with no children.



## Example 1:
~~~
Input: root = [3,9,20,null,null,15,7]
Output: 2
~~~

## Example 2:
~~~
Input: root = [2,null,3,null,4,null,5,null,6]
Output: 5
~~~

{% highlight java %}
package leetcode;

import java.util.LinkedList;
import java.util.Queue;

/**
 * Created by mkhwang on 2021/04/21
 * Email : orange2652@gmail.com
 * Github : https://github.com/myeongkwonhwang
 */
public class MinimumDepthOfNinaryTree {

    class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode() {}
        TreeNode(int val) { this.val = val; }
        TreeNode(int val, TreeNode left, TreeNode right) {
            this.val = val;
            this.left = left;
            this.right = right;
        }
    }
    class Solution {
        public int minDepth(TreeNode root) {
            if(root == null) return 0;
            Queue<Node> q = new LinkedList<>();
            q.add(new Node(root, 1));
            while(!q.isEmpty()){
                Node node = q.poll();
                int depth = node.depth;
                if(node.treeNode.left == null && node.treeNode.right == null) return depth;
                if(node.treeNode.left != null) q.add(new Node(node.treeNode.left, depth+1));
                if(node.treeNode.right != null) q.add(new Node(node.treeNode.right, depth+1));
            }
            return 0;
        }
    }
    class Node {
        TreeNode treeNode;
        int depth;
        Node(TreeNode node, int depth){
            this.treeNode = node;
            this.depth = depth;
        }
    }

}

{% endhighlight %} 
