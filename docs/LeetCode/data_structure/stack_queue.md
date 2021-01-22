# 栈及DFS和队列及BFS

## 一 简介

栈的特点是后入先出

![image.png](https://img.fuiboom.com/img/stack.png)

根据这个特点可以临时保存一些数据，之后用到依次再弹出来，常用于 DFS 深度搜索

队列一般常用于 BFS 广度搜索，类似一层一层的搜索

## 二 Stack 栈
### (一) 辅助单调栈

[min-stack](https://leetcode-cn.com/problems/min-stack/)

> 设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

思路：用两个栈实现，一个最小栈始终保证最小值在顶部

min_stack等价于遍历stack所有元素，把升序的数字都删除掉，留下一个从**栈底到栈顶降序的栈（单调栈）**。

```java
class MinStack {

    /** initialize your data structure here. */
    LinkedList<Integer> stack;
    LinkedList<Integer>  minstack;
    public MinStack() {
        stack=new LinkedList<>();
        minstack=new LinkedList<>();
    }
    
    public void push(int x) {
        stack.addFirst(x);
        if(minstack.isEmpty() || x <= minstack.peekFirst())
            minstack.push(x);
    }
    
    public void pop() {
        int i=stack.removeFirst();
        if(!minstack.isEmpty()&&i==minstack.peekFirst()){
            minstack.removeFirst();
        }
    }
    
    public int top() {
        return stack.peekFirst();
    }
    
    public int getMin() {
        return minstack.peekFirst();
    }
}
}
```
[716. 最大栈](https://leetcode-cn.com/problems/max-stack/)

思路：维护一个单调递增栈
```java
class MaxStack {

    /** initialize your data structure here. */
    LinkedList<Integer> stack;
    LinkedList<Integer>  maxstack;
    public MaxStack() {
        stack=new LinkedList<>();
        maxstack=new LinkedList<>();
    }
    
    public void push(int x) {
        stack.addFirst(x);
        if(maxstack.isEmpty() || x >= maxstack.peekFirst())
            maxstack.push(x);
    }
    
    public int pop() {
        int i=stack.removeFirst();
        if(!maxstack.isEmpty()&&i==maxstack.peekFirst()){
            maxstack.removeFirst();
        }
        return i;
    }
    
    public int top() {
       return stack.peekFirst();
    }
    
    public int peekMax() {

     return maxstack.peekFirst();
      
    }
    
     public int popMax() {
        int max = peekMax();
        Stack<Integer> buffer = new Stack();
        //找到删除元素
        while (top() != max) 
            buffer.push(pop());
        //删除元素    
        pop();
        //删除元素以后的元素重新入栈
        while (!buffer.isEmpty()) 
            push(buffer.pop());
        return max;
    }
}
```
### (二) 栈模拟运算
[evaluate-reverse-polish-notation](https://leetcode-cn.com/problems/evaluate-reverse-polish-notation/)

> **波兰表达式计算** > **输入:** ["2", "1", "+", "3", "*"] > **输出:** 9
> **解释:** ((2 + 1) \* 3) = 9

思路：用栈保存原来的元素，遇到+-*/取出运算，再存入结果，重复这个过程

```java
class Solution {
      public int evalRPN(String[] tokens) {
        LinkedList<Integer> stack =  new LinkedList<>();
        for(int i=0;i<tokens.length;i++){
            if(tokens[i].equals("+")){
                int o2=stack.removeFirst();
                int o1=stack.removeFirst();
                stack.addFirst(o1+o2);
            }else if(tokens[i].equals("-")){
                int o2=stack.removeFirst();
                int o1=stack.removeFirst();
                stack.addFirst(o1-o2);
            }else if(tokens[i].equals("*")){
                int o2=stack.removeFirst();
                int o1=stack.removeFirst();
                stack.addFirst(o1*o2);
            }else if(tokens[i].equals("/")){
                int o2=stack.removeFirst();
                int o1=stack.removeFirst();
                stack.addFirst(o1/o2);
            }else{
                stack.addFirst(Integer.parseInt(tokens[i]));
            }
        }
        return stack.peekFirst();
    }
}
```



## 三 DFS深度优先（图）

### [基础知识](https://blog.csdn.net/lightupworld/article/details/107186331?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_v2~rank_v29-1-107186331.nonecase&utm_term=java%20%E5%9B%BEdfs%E6%A8%A1%E6%9D%BF&spm=1000.2123.3001.4430)

### DFS 递归模板-1(类似回溯模板)
> 判断路径是否存在，但不能找出最短路径
> 
> DFS 实际上是靠递归的堆栈记录⾛过的路径
> 
> 要找到最短路径，肯定得把⼆叉树中所有树杈都探索完才能对⽐出最短的路径有多⻓
```java
/*
 * Return true if there is a path from cur to target.
 * 如果找到路径，则返回true,并不一定是最短路径
 */
//
boolean DFS(Node cur, Node target, Set<Node> visited) {
	//递归终止条件，找到节点
    if cur is target
		return true;
	//遍历cur的所有邻居节点
    for (next : each neighbor of cur) {
        if (next is not in visited) {
            add next to visted;
            if DFS(next, target, visited) == true
				return true;
			//不需要visited状态撤销，因为visited是参数，不是全局变量
        }
    }
    return false;
}
```

[岛屿数量](https://leetcode-cn.com/problems/number-of-islands/solution/)

>给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

>岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

思路：通过深度搜索遍历可能性（注意标记已访问元素）
```java
class Solution {
    int[][] visited;
    public int numIslands(char[][] grid) {
        visited=new int[grid.length][grid[0].length];
        int count=0;
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]=='1'&&visited[i][j]==0){
                    DFS(grid,i,j);
                    count++;
                }
            }
        }
        return count;
    }

    public void DFS(char[][] grid,int i,int j){
        //递归终止条件，找到节点
        if(grid[i][j]=='0'||visited[i][j]==1){
		    return;
        }else{
            visited[i][j]=1;
        }    

	    //遍历所有邻居节点
        //上
        if(i-1>=0){
          DFS(grid,i-1,j);
        }
        //下
        if(i+1<=grid.length-1){
          DFS(grid,i+1,j);
        }
        //左
        if(j-1>=0){
          DFS(grid,i,j-1);
        }
        //右
        if(j+1<=grid[0].length-1){
          DFS(grid,i,j+1);
        }
    
    }
}
```

[clone-graph](https://leetcode-cn.com/problems/clone-graph/)

> 给你无向连通图中一个节点的引用，请你返回该图的深拷贝（克隆）。

```java
/*
// Definition for a Node.
class Node {
    public int val;
    public List<Node> neighbors;
    
    public Node() {
        val = 0;
        neighbors = new ArrayList<Node>();
    }
    
    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<Node>();
    }
    
    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}
*/

class Solution {
    HashMap<Node,Node> visited= new HashMap<>();
    public Node cloneGraph(Node node) {
        //特例
        if(node==null){
            return node;
        }

        //递归终止条件 如果visited存在node的克隆结点则返回克隆结点
        if(visited.containsKey(node)){
            return visited.get(node);
        }

        Node cloneNode=new Node(node.val);
        visited.put(node,cloneNode);
              
	    //遍历node的所有邻居节点，设置好cloneNode结点的克隆结点
        List<Node> neighbors=node.neighbors;
        List<Node> newNeighbors=cloneNode.neighbors;
        for(Node neighbor:neighbors){
            Node newNeighbor=cloneGraph(neighbor);
            newNeighbors.add(newNeighbor);
        }
       
        return cloneNode;
    }
}
```

[494. 目标和](https://leetcode-cn.com/problems/target-sum/)

> 给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。
> 现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。
> 返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

思路 类似于回溯 DFS有状态撤回
```java
class Solution {
    int target;
    int count=0;
    public int findTargetSumWays(int[] nums, int S) {
        target=S;
        DFS(nums,0,0);
        return count;
    }

    public void DFS(int[] nums,int sum,int index){
        //终止条件
        if(index==nums.length){
            if(sum==target) {
                count++;
            }
            return;
        }

        //遍历所有邻居节点+/-
        //+
        sum=sum+nums[index];
        DFS(nums,sum,index+1);

        //状态撤回
        sum-=nums[index];
        //-
        sum=sum-nums[index];
        DFS(nums,sum,index+1);


    }
}
```

#### [399. 除法求值](https://leetcode-cn.com/problems/evaluate-division/)

> 给你一个变量对数组 equations 和一个实数值数组 values 作为已知条件，其中 equations[i] = [Ai, Bi] 和 values[i] 共同表示等式 Ai / Bi = values[i] 。每个 Ai 或 Bi 是一个表示单个变量的字符串。
>
> 另有一些以数组 queries 表示的问题，其中 queries[j] = [Cj, Dj] 表示第 j 个问题，请你根据已知条件找出 Cj / Dj = ? 的结果作为答案。

利用图的深度遍历

```java
class Solution {
    class Edge{
        String from;
        String to;
        double val;

        public Edge(String from,String to,double val){
            this.from=from;
            this.to=to;
            this.val=val;
        }
    }
    HashMap<String,List<Edge>> nodeEdges=new HashMap<>();
    double[] mRes;
    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        //1 构建加权有向图
        for(int i=0;i<equations.size();i++){
            List<String> edge=equations.get(i);
            String v1=edge.get(0);
            String v2=edge.get(1);
            double val=values[i];

            //向图里添加两条边
            // 添加到 v1->v2 的边
            List<Edge> v1Edges = nodeEdges.get(v1);
            if (v1Edges == null) {
                v1Edges = new ArrayList<>();
                nodeEdges.put(v1, v1Edges);
            }
            v1Edges.add(new Edge(v1, v2, val));
            // 添加到 v2->v1 的边
            List<Edge> v2Edges = nodeEdges.get(v2);
            if (v2Edges == null) {
                v2Edges = new ArrayList<>();
                nodeEdges.put(v2, v2Edges);
            }
            v2Edges.add(new Edge(v2, v1, 1.0 / val));
        }

        // 2. dfs 搜索数据
         mRes = new double[queries.size()];
        List<String> visited = new ArrayList<>();
        for (int i = 0; i < queries.size(); i++) {
            List<String> query = queries.get(i);
            String start = query.get(0);
            String dest = query.get(1);
            visited.clear();
            mRes[i] = dfs(start, dest, visited);
        }
        return mRes;
        
    }

     private double dfs(String start, String dest, List<String> visited) {
            // 验证是否存顶点
            if (!nodeEdges.containsKey(start) || !nodeEdges.containsKey(dest)) {
                return -1.0;
            }
            visited.add(start);
            //递归终止条件
            if (start.equals(dest)) {
                return 1.0;
            }
            // 获取 start 顶点的边
            List<Edge> startEdges = nodeEdges.get(start);
            if (startEdges == null || startEdges.isEmpty()) {
                return -1.0;
            }
            // 深度优先遍历集合
            for (Edge edge : startEdges) {
                //遍历过该节点则跳过
                if (visited.contains(edge.to)) {
                    continue;
                }
                double res = dfs(edge.to, dest, visited);
                if (res != -1.0) {
                    return res * edge.val;
                }
            }
            return -1.0;
        }

}
```



### DFS 非递归模板-2(栈实现,类似与树的非递归中序遍历)

[动画](https://leetcode-cn.com/leetbook/read/queue-stack/gro21/)

利用栈进行 DFS 非递归搜索模板

```
// 伪代码
boolean DFS(int root, int target) {
    Set<Node> visited;
    Stack<Node> s;
    add root to s;
    while (s is not empty) {
        Node cur = the top element in s;
		//循环终止条件，找到节点
        if cur is target
			return true;
	    //选一方向的邻居一直入栈，cur不断深入，直到这个方向全入栈
        for (Node next : the one direction neighbors of cur) {
            if (next is not in visited) {
                add next to s;
                add next to visited;
            }
			cur=next;
        }
		//当前节点出栈
        remove cur from s;
		//换一个方向
    }
    return false;
}
```

[binary-tree-inorder-traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

> 给定一个二叉树，返回它的*中序*遍历。

```java
// 思路：通过stack 保存已经访问的元素，用于原路返回
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        if (root == null) {
            return new LinkedList<>();
        }
        List<Integer> res = new LinkedList<>();
        LinkedList<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
        while(node != null || !stack.isEmpty()) {
            while (node != null) {
                stack.addLast(node);
                node = node.left;
            }
            node = stack.removeLast();
            res.add(node.val);
            node = node.right;
        }
        return res;
    }
}
```









## 四 Queue 队列


[implement-queue-using-stacks](https://leetcode-cn.com/problems/implement-queue-using-stacks/)

> 使用栈实现队列

思路：一个栈模拟进队列，一个栈模拟出队列

```java
class MyQueue {

    /** Initialize your data structure here. */
    LinkedList<Integer> instack;
    LinkedList<Integer> outstack;
    public MyQueue() {
         instack=new LinkedList<>();
         outstack=new LinkedList<>();
    }
    
    /** Push element x to the back of queue. */
    public void push(int x) {
        instack.addFirst(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    public int pop() {
        if(!outstack.isEmpty()){
            return outstack.removeFirst();
        }
        while(!instack.isEmpty()){
            outstack.addFirst(instack.removeFirst());
        }
        return outstack.removeFirst();
    }
    
    /** Get the front element. */
    public int peek() {
         if(!outstack.isEmpty()){
            return outstack.peekFirst();
        }
        while(!instack.isEmpty()){
            outstack.addFirst(instack.removeFirst());
        }
        return outstack.peekFirst();

    }
    
    /** Returns whether the queue is empty. */
    public boolean empty() {
        return instack.isEmpty()&&outstack.isEmpty();
    }
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue obj = new MyQueue();
 * obj.push(x);
 * int param_2 = obj.pop();
 * int param_3 = obj.peek();
 * boolean param_4 = obj.empty();
 */
```

## 五 BFS（图）
### [基础知识](https://leetcode-cn.com/leetbook/read/queue-stack/kyozi/)

### BFS模板-1 (类似树层次遍历，队列实现)
BFS 的两个主要方案：遍历或找出**最短路径**。

```java
/**
 * 返回根节点和目标节点之间最短路径的长度。
 */
int BFS(Node root, Node target) {
    Queue<Node> queue;  // 存储所有等待处理的节点
    int step = 0;       // 从根到当前节点所需的步数，也可以理解为层数
    // initialize
    add root to queue;
    // BFS
    while (queue is not empty) {
        step = step + 1;
        //迭代队列中已经存在的节点，相当于对每一层的处理
        int size = queue.size();
        for (int i = 0; i < size; ++i) {
            Node cur = the first node in queue;
            if cur is target
				return step； 
            for (Node next : the neighbors of cur) {
                add next to queue;
            }
            remove the first node(cur) from queue;
        }
    }
    return -1;          // there is no path from root to target
}
```

常用于 BFS 宽度优先搜索
[二叉树层次遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)


```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
   public List<List<Integer>> levelOrder(TreeNode root) {
       
        List<List<Integer>> result=new LinkedList<>();
		//特例
        if (root==null){
            return result;
        }
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.addLast(root);
        while (!queue.isEmpty()){
            //该层的节点数量
            int size = queue.size();
            LinkedList<Integer> level = new LinkedList<>();
            for (int i = 0; i < size; i++) {
                TreeNode treeNode = queue.removeFirst();
                level.add(treeNode.val);
                if (treeNode.left!=null){
                    queue.addLast(treeNode.left);
                }
                if (treeNode.right!=null){
                    queue.addLast(treeNode.right);
                }
            }
            result.add(level);
        }
        return result;
    }
}
```
### BFS模板-2 (添加hash，增加访问控制)
有时，确保我们永远不会访问一个结点两次很重要。否则，我们可能陷入无限循环。如果是这样，我们可以在上面的代码中添加一个**哈希集**来解决这个问题。

**有两种情况你不需要使用哈希集：**
* 你完全确定没有循环，例如，在树遍历中；
* 你确实希望多次将结点添加到队列中。

```java
/**
 * 返回根节点和目标节点之间最短路径的长度。
 */
int BFS(Node root, Node target) {
    Queue<Node> queue;  // 存储所有等待处理的节点
    Set<Node> used;     // 存储所有使用的节点*****
    int step = 0;       // 从根到当前节点所需的步数，也可以理解为层数
    // initialize
    add root to queue;
    add root to used;
    // BFS
    while (queue is not empty) {
        step = step + 1;
        // 迭代队列中已经存在的节点，相当于对每一层的处理
        int size = queue.size();
        for (int i = 0; i < size; ++i) {
            Node cur = the first node in queue;
            if cur is target
				return step；
            for (Node next : the neighbors of cur) {
                if (next is not in used) {
                    add next to queue;
                    add next to used;
                }
            }
            remove the first node（cur）from queue;
        }
    }
    return -1;          // there is no path from root to target
}
```

### 多源BFS
* 对于 「Tree 的 BFS」 （典型的「单源 BFS])，首先把 root 节点入队，再一层一层无脑遍历就行了
* 「图 的 BFS」 （「多源 BFS」）**必须得标志是否访问过**，省略超级节点(假想)，先将第一层入队，再开始BFS模板2。
[01-matrix](https://leetcode-cn.com/problems/01-matrix/)

> 给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
> 两个相邻元素间的距离为 1


```java
class Solution {

    public int[][] updateMatrix(int[][] matrix) {
        int[][] result=new int[matrix.length][matrix[0].length];
        int MAX_TEMP = Integer.MAX_VALUE;
        LinkedList<int[]> queue=new LinkedList<>();  // 存储所有等待处理的节点
        int[][] visited =new int[matrix.length][matrix[0].length];  // 存储所有使用过的节点
        for(int i=0;i<matrix.length;i++){
            for(int j=0;j<matrix[0].length;j++){
                if(matrix[i][j]==0){
                    result[i][j]=0;
                        //首先放入第一层--多源
                        queue.addLast(new int[]{i,j});
                        visited[i][j]=1;
                }else{
                        result[i][j]=MAX_TEMP;
                }
            }
        }

    // BFS
    int step=0;    
    while (!queue.isEmpty()) {
        step = step + 1;
        // 迭代队列中已经存在的节点，相当于对每一层的处理
        int size = queue.size();
        for (int k = 0; k < size; ++k) {
            int[] cur=queue.removeFirst();
            int m=cur[0];
            int n=cur[1];
 
             //遍历所有邻居节点
             //上
            if(m-1>=0&&visited[m-1][n]==0){
                result[m-1][n] = step;
                visited[m-1][n]=1;
                queue.addLast(new int[]{m-1,n});
            }
            //下
            if(m+1<=matrix.length-1&&visited[m+1][n]==0){
                result[m+1][n] = step;
                visited[m+1][n]=1;
                queue.addLast(new int[]{m+1,n});
             }
            //左
            if(n-1>=0&&visited[m][n-1]==0){
                result[m][n-1] = step;
                visited[m][n-1]=1;
                queue.addLast(new int[]{m,n-1});
            }
            //右
            if(n+1<=matrix[0].length-1&&visited[m][n+1]==0){
             result[m][n+1] =step;
                visited[m][n+1]=1;
               queue.addLast(new int[]{m,n+1});
            }    
            
        }
    }
        return result;
    }


}
```

## 总结

- 熟悉栈的使用场景
  - 后出先出，保存临时值
  - 利用栈深度搜索
- 熟悉队列的使用场景
  - 利用队列 BFS 广度搜索


