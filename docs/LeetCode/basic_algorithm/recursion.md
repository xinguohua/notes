# 递归

## 介绍

将大问题转化为小问题，通过递归依次解决各个小问题

## 模板

1. **递归的函数要干什么？** 

   <font color="#dd0000">递归函数</font>解决一个由大问题拆分而来的<font color="#dd0000">小问题</font>====》（构造）函数的输入参数和返回值。

2. **递归停止的条件是什么？** 

   通常用if语句

3. **本级递归应该做什么?**

   只考虑一层的状况

4. **从本层递归到下一层的关系是什么？**

   <font color="#dd0000">实现递归</font>

5. **递归返回后的操作（可能有）**

```
//1 递归的函数要干什么确定参数和返回值
public void f()
{	
	//2 递归终止条件
     if(符合边界条件)
    {
      return;
    }
      //3 本级递归做什么
      xxxx//一般情况下的操作
     //4 本层递归到下一层的关系，实现递归
     f();
	//5 递归返回后的操作
}
```

## 示例

[reverse-string](https://leetcode-cn.com/problems/reverse-string/)

> 编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组  `char[]`  的形式给出。


```java
class Solution {
    public void reverseString(char[] s) {
        //调用递归函数
        //1 递归函数的功能：实现反转两个指针的位置(小问题)
        //输入参数:字符串数组的左指针，右指针，字符串数组 
        //返回值:空
        swap(0, s.length-1, s);
    }
    
    public void swap(int start, int end, char[] s) {
        //2 递归终止条件
        if(start >= end){
            return;
        }
        //3 本级递归的操作
        char temp = s[start];
        s[start] = s[end];
        s[end] = temp;
        //4 与下一级递归的关系，指针变化，实现递归✳
        swap(start+1, end-1, s);
    }
}
```

[swap-nodes-in-pairs](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

> 给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。
> **你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

直接迭代空间复杂度小，速度快
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {

        //1 递归函数的功能：交换pair两个节点位置（小问题）
        //输入头结点 
        //返回的是交换后链表的头结点
        ListNode result=Recursion(head);

        return result;

    }

    private ListNode Recursion(ListNode head) {
        //2 递归的终止条件 pair没有或者只有一个，直接返回head
        if (head==null||head.next==null){
            return head;
        }
        //3 本级的递归操作
        //head-->next-->nextPairStart
        //取到下一个pair开始的节点
        ListNode nextPairStart=head.next.next;
        //获取第二个节点
        ListNode next=head.next;
        //第二个节点连接第一个节点
        next.next=head;
        //第一个节点连递归返回来的节点

        //4 与下一级递归的关系，下一级返回的节点相连，实现递归✳
        head.next=Recursion(nextPairStart);

        return next;
    }
}
```

[unique-binary-search-trees-ii](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

> 给定一个整数 n，生成所有由 1 ... n 为节点所组成的二叉搜索树。

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
  public List<TreeNode> generateTrees(int n) {
        //特例判定，返回空的根节点列表
        if(n < 1)
            return new ArrayList<>();
        //1 递归函数的功能：返回区间内构成二叉搜索树所有根节点的列表
        //输入: 区间的范围[start,end]
        //输出：根节点的列表
        List<TreeNode> rootList = helper(1, n);
        return rootList;
    }

    public List<TreeNode> helper(int start, int end){
        //存放区间内所有根节点的列表（即返回值）
        List<TreeNode> list = new ArrayList<>();
        //2 递归的终止条件
        if(start > end){
            // 如果当前子树为空，不加null行吗？
            list.add(null);
            return list;
        }

        //3 本次递归的操作
        //遍历区间所有的节点构造二叉树，返回根节点并添加到结果列表中
        for(int i = start; i <= end; i++){

            // 想想为什么这行不能放在这里，而放在下面？
            // TreeNode root = new TreeNode(i);

            //4 与下一级递归的关系，抛弃根节点寻找左右子树列表，建立递归
            List<TreeNode> left = helper(start, i-1);
            List<TreeNode> right = helper(i+1, end);

            // 5 递归返回后的操作，构建对应根节点的二叉搜索树，并将根节点加入列表
            // 固定左孩子，遍历右孩子
            for(TreeNode l : left){
                for(TreeNode r : right){
                    TreeNode root = new TreeNode(i);
                    root.left = l;
                    root.right = r;
                    list.add(root);
                }
            }
        }
        return list;
    }
}
```

## 递归+备忘录

[fibonacci-number](https://leetcode-cn.com/problems/fibonacci-number/)

> 斐波那契数，通常用  F(n) 表示，形成的序列称为斐波那契数列。该数列由  0 和 1 开始，后面的每一项数字都是前面两项数字的和。也就是：
> F(0) = 0,   F(1) = 1
> F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
> 给定  N，计算  F(N)。

迭代解法，自底向上（动态规划解法）
```java
class Solution {
	public int fib(int N) {
        if (N <= 1) {
            return N;
        }
        return memoize(N);
    }
    private int memoize(int n) {
        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 1;
        for (int i = 2; i <= n; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
}
```

递归解法，自顶向下**（记忆化递归）**
```java
class Solution {
    //记忆化缓存
    private Integer[] cache = new Integer[31];
    public int fib(int N) {
        if (N <= 1) {
            return N;
        }
        cache[0] = 0;
        cache[1] = 1;
        //1 递归功能：输入N返回fib值
        return memoize(N);
    }
    private int memoize(int n) {
        //2 递归终止条件：缓存有fib值
        if (cache[n] != null) {
            return cache[n];
        }
        //3 本级递归要做的事情
        //4 建立递归关系
        cache[n] = memoize(n - 1) + memoize(n - 2);
        //5 递归后要做的事情
        return memoize(n);
    }
}
```

## 练习

