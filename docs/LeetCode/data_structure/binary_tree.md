# 二叉树模板

## 1 二叉树遍历

**前序遍历**：**先访问根节点**，再前序遍历左子树，再前序遍历右子树
**中序遍历**：先中序遍历左子树，**再访问根节点**，再中序遍历右子树
**后序遍历**：先后序遍历左子树，再后序遍历右子树，**再访问根节点**

注意点

- 以根访问顺序决定是什么遍历
- 左子树都是优先右子树


#### 树结构
```java
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}
```
### 1.1 递归遍历

#### 前序递归

```java
public class PreOrderRecursive {
    public void preOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        System.out.println(root.val);
        preOrder(root.left);
        preOrder(root.right);
    }
}
```

#### 中序递归
```java
public class InOrderRecursive {
    public void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.left);
        // 在递归中间写操作
        System.out.println(root.val);
        inOrder(root.right);
    }
}
```
#### 后序递归
```java
public class InOrderRecursive {
    public void inOrder(TreeNode root) {
        if (root == null) {
            return;
        }
        inOrder(root.left);
        inOrder(root.right);
        // 在递归后面写操作
        System.out.println(root.val);
    }
}
```
### 1.2 非递归遍历

#### 前序非递归
* 用Deque模拟栈，因为Stack类是遗留类，不推荐使用。所有需要使用栈的地方都用Deque来模拟。
* 因为结果是根-->左-->右，所以放入栈中是根-->右-->左
```java
class Solution {
    public List<Integer> preOrderTraversal(TreeNode root) {
        if (root == null) {
            return new LinkedList<>();
        }
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.addFirst(root);
        while(!stack.isEmpty()) {
            TreeNode node = stack.removeFirst();
            res.add(node.val);
            if (node.right != null) {
                stack.addFirst(node.right);
            }
            if (node.left != null) {
                stack.addFirst(node.left);
            }
        }
        return res;
    }
}
```

#### 中序非递归
中序非递归遍历是先遍历最左节点，然后层层向上回溯。
* 有两个有生力量node和stack都为空则结束
* node不为空一直往其左孩子走下去，将左孩子入栈，直到该结点没有左孩子，则访问这个结点，如果这个结点有右孩子，则将其右孩子入栈，重复找左孩子的动作。
* stack不为空则转向右孩子
```java
class Solution {
    public List<Integer> inOrderTraversal(TreeNode root) {
        if (root == null) {
            return new LinkedList<>();
        }
        List<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        TreeNode node = root;
		//有两个有生力量node和stack都为空则结束
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

#### 后序非递归
后序非递归遍历，跟前序非递归有些类似，不过后序是先访问跟节点，然后左子节点，再访问右子节点，依次压入栈；
从栈里取出时，是后序遍历的反序，需要翻转。这里利用Deque的性质，可每次插入到链头，相当于翻转。
```java
class Solution {
    public List<Integer> postrderTraversal(TreeNode root) {
        if (root == null) {
            return new LinkedList<>();
        }
        LinkedList<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new LinkedList<>();
        stack.addFirst(root);
        while(!stack.isEmpty()) {
            TreeNode node = stack.removeFirst();
            res.addFirst(node.val);
            if (node.left != null) {
                stack.addFirst(node.left);
            }
            if (node.right != null) {
                stack.addFirst(node.right);
            }
        }
        return res;
    }
}
```
## 2 BFS 层次遍历

```java
public class Solution {
    public List<Integer> levelOrder(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        while(!queue.isEmpty()) {
            // size记录当前层有多少元素（遍历当前层，再添加下一层）
            int size = queue.size();
            for (int i = 0; i < size; ++i) {
                TreeNode node = queue.poll();
                res.add(node.val);
                if (node.left != null) {
                    queue.add(node.left);
                }
                if (node.right != null) {
                    queue.add(node.right);
                }
            }
        }
        return res;
    }
}
```

## 3 DFS
### 3.1 DFS 深度搜索-从上到下

```java
public class Solution {
    public List<Integer> dfsUpToDown(TreeNode root) {
        List<Integer> res = new LinkedList<>();
        dfs(root, res);
        return res;
    }
    
    public void dfs(TreeNode node, List<Integer> res) {
        if (node == null) {
            return;
        }
        res.add(node.val);
        dfs(node.left, res);
        dfs(node.right, res);
    }
}
```

### 3.2 DFS 深度搜索-从下向上（分治法）

```java
public class Solution {
    public List<Integer> prerderTraversal(TreeNode root) {
        return divideAndConquer(root);
    }

    public List<Integer> divideAndConquer(TreeNode node) {
        List<Integer> result = new LinkedList<>();
        if (node == null) {
            return null;
        }
        // 分治
        List<Integer> left = divideAndConquer(node.left);
        List<Integer> right = divideAndConquer(node.right);
        // 合并结果
        result.add(node.val);
      	if (left != null) {
            result.addAll(left);
        }
        if (right != null) {
            result.addAll(right);
        }
        return result;
    }
}
```

注意点：

> DFS 深度搜索（从上到下） 和分治法区别：前者一般将最终结果通过指针参数传入，后者一般递归返回结果最后合并



## 4 分治法应用（二叉树递归采用分治的思想）

* 先分别处理局部，再合并结果。
* 本制是递归，分治法加上了分段处理与合并的思想。

适用场景

- 二叉树相关问题
- 归并排序
- 快速排序

分治法模板

- 递归返回条件
- 分段处理
- 合并结果

```java
// 模板
public class Solution {
    public ResultType traversal(TreeNode root) {
        if (root == null) {
            // do something and return
        }

        // Divide
        ResultType left = traversal(root.left);
        ResultType right = traversal(root.right);

        // Conquer
        ResultType result = Merge from left and right

        return result;
    }
}
```

### 4.1 二叉树相关问题

```java
// 通过分治法遍历二叉树
public class Solution {
    public List<Integer> prerderTraversal(TreeNode root) {
        return divideAndConquer(root);
    }

    public List<Integer> divideAndConquer(TreeNode node) {
        List<Integer> result = new LinkedList<>();
        if (node == null) {
            return null;
        }
        // 分治
        List<Integer> left = divideAndConquer(node.left);
        List<Integer> right = divideAndConquer(node.right);
        // 合并结果
        result.add(node.val);
        result.addAll(left);
        result.addAll(right);
        return result;
    }
}
```

### 4.2 归并排序  

```java
public class Solution {
    public int[] mergeSortRoot(int[] nums) {
        mergeSort(nums, 0 , nums.length - 1);
        return nums;
    }

    private void mergeSort(int[] nums, int l, int r) {
        if (l < r) {
            int mid = (l + r) / 2;
            // 分治
            mergeSort(nums, l, mid);
            mergeSort(nums, mid + 1, r);
            // 合并
            merge(nums, l, mid, r);
        }
    }

    private void merge(int[] nums, int l, int mid, int r) {
        int i = l, j = mid + 1, k = l;
        int[] tmp = new int[nums.length];
        while (i <= mid && j <= r) {
            if (nums[i] > nums[j]) {
                tmp[k++] = nums[j++];
            } else {
                tmp[k++] = nums[i++];
            }
        }
        while (i <= mid) {
            tmp[k++] = nums[i++];
        }
        while (j <= r) {
            tmp[k++] = nums[j++];
        }
        for (i = l; i <= r; i++) {
            nums[i] = tmp[i];
        }
    }
}
```

注意点

> 递归需要返回结果用于合并

### 4.3 快速排序  

```java
public class Solution {
    public static void sort(int[] arr){
        // 思路：把一个数组分为左右两段，左段小于右段，类似分治法没有合并过程
        quickSort(arr,0,arr.length-1);
    }

    /**
     * 使用递归实现快速排序
     * @param arr
     * @param start
     * @param end
     */
    public static void quickSort(int[] arr,int start,int end){
        //递归终止条件
        if (start >= end){
            return;
        }

        //获取分区的基准点的数组下标pivot,pivot左边的比arr[pivot]小，右边的比arr[pivot]大
        int pivot = partition(arr,start,end);
        //使用递归的方式继续进行分区
        quickSort(arr,start,pivot-1); //处理左侧数组
        quickSort(arr,pivot+1,end); //处理右侧数组

    }

    /**
     * 进行分区操作
     * 对arr[start...end]部分进行partition操作
     * 返回pivot, 使得arr[start...pivot-1] < arr[pivot] ; arr[pivot]<arr[pivot+1...end]
     */
    private static int partition(int[] arr,int start ,int end){
        //随机在arr[start...end]的范围中, 选择一个数值作为标定点pivot
        //之所以要随机选择,是为了防止在出现大规模近乎有序的数据时若只取左边的值可能会
        //造成快速排序算法退化到O(N²)级别,与二叉树退化成链表是同样的道理.
        //产生的随机数0=<random<r+1
        //最右侧存放pivot
        swap(arr,end,(int)(Math.random()*(end-start+1))+start);
        //确定分组元素，end存放的是基准
        int target = arr[end];
        //进行分区操作,使用得 arr[start...j]< target(j++) < arr(j...i) i是当前正在判断的元素
        int j = start;
        //注意i需要从start起,end-1结束不包括end，end存放的是基准元素
        for (int i = start; i < end ; i++) {
            //当前元素比基准点元素小,移动到target的左侧.
            //当前元素比基准点元素大,无需移动.
            if(arr[i]< target){
                swap(arr,j,i);
                j++;//j往后移动一位
            }
        }

        //最后将基准点移动到j的位置,形成arr[l...j-1] < arr[j] < arr[j+1...r]
        swap(arr,j,end);
        //返回基准点下标
        return j;
    }


    public static void swap(int[] arr, int i, int j) {
        int t = arr[i];
        arr[i] = arr[j];
        arr[j] = t;
    }
}
```

注意点：

> 快排由于是原地交换所以没有合并过程
> 传入的索引是存在的索引（如：0、length-1 等），越界可能导致崩溃

# 二叉树常见题目示例
## （一） 分治递归的题（二叉树递归的主要思想）

#### maximum-depth-of-binary-tree

[maximum-depth-of-binary-tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

> 给定一个二叉树，找出其最大深度。

思路：分治法

```java
// 计算maxDepth(root.left)，maxDepth(root.right)就是分治，
// Math.max(maxDepth(root.left), maxDepth(root.right)) + 1就是合并的过程
class Solution {
    public int maxDepth(TreeNode root) {
        //递归返回条件
        if (root==null){
            return 1;
        }
        //分治
        //分段处理
        int leftDepth=maxDepth(root.left);
        int rightDepth=maxDepth(root.right);
        //合并结果
        return Math.max(leftDepth,rightDepth)+1;
    }
}


```

#### balanced-binary-tree

[balanced-binary-tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

> 给定一个二叉树，判断它是否是高度平衡的二叉树。

思路：分治法，左边平衡 && 右边平衡 && 左右两边高度 <= 1，
遍历一遍，如果有左右两边高度 > 1，则不是高度平衡的二叉树。

```java
class Solution {
public boolean isBalanced(TreeNode root) {
        //递归返回条件
        if (root==null){
            return true;
        }
        //分治
        boolean left = isBalanced(root.left);
        boolean right = isBalanced(root.right);
        boolean now=Math.abs(getHeight(root.left)-getHeight(root.right))>1?false:true;
        //合并
        return left&&right&&now;
    }

    private int getHeight(TreeNode root) {
        if (root==null){
            return 0;
        }
        return Math.max(getHeight(root.right),getHeight(root.left))+1;
    }
}


```

#### lowest-common-ancestor-of-a-binary-tree

[lowest-common-ancestor-of-a-binary-tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

> 给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

思路：分治法，有左子树的公共祖先或者有右子树的公共祖先，就返回子树的祖先，否则返回根节点

```java
class Solution {
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
		//递归返回条件 找不到，找到
        if(root == null || root == p || root == q) 
				return root;
		//分治：在左子树中找，在右子树中找
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
		//归并（回溯）
        if(left == null && right == null) return null; // 1.
        if(left == null) return right; // 3.
        if(right == null) return left; // 4.
        return root; // 2. if(left != null and right != null)
    }
}


}
```
## （二） BFS 层次遍历的题

#### binary-tree-level-order-traversal

[binary-tree-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> 给你一个二叉树，请你返回其按  **层序遍历**  得到的节点值。 （即逐层地，从左到右访问所有节点）

思路：用一个队列记录一层的元素，然后扫描这一层元素添加下一层元素到队列（一个数进去出来一次，所以复杂度 O(logN)）

```java
import java.util.Queue;
class Solution {
   public List<List<Integer>> levelOrder(TreeNode root) {
       
        List<List<Integer>> result=new LinkedList<>();
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

#### binary-tree-level-order-traversal-ii

[binary-tree-level-order-traversal-ii](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

> 给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）

思路：在层级遍历的基础上，翻转一下结果即可，这里利用Collections工具类

```java
class Solution {
   public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> result=new LinkedList<>();
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
        Collections.reverse(result);
        return result;
    }
}
```

#### binary-tree-zigzag-level-order-traversal

[binary-tree-zigzag-level-order-traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

> 给定一个二叉树，返回其节点值的锯齿形层次遍历。Z 字形遍历

```java
class Solution {
   public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        List<List<Integer>> result = new LinkedList<>();
        if (root == null) {
            return result;
        }
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.addLast(root);
        boolean flag = true;
        while (!queue.isEmpty()) {
            int size = queue.size();
            LinkedList<Integer> level = new LinkedList<>();

            for (int i = 0; i < size; i++) {
                TreeNode treeNode = queue.removeFirst();
                // flag 为false, 需要从右往左遍历，这里直接插入首部，实现翻转的效果。
                // 也可以再插入完一层后，直接reverse()
                if (flag) {
                    level.addLast(treeNode.val);
                } else {
                    level.addFirst(treeNode.val);
                }
                if (treeNode.left != null) {
                    queue.addLast(treeNode.left);
                }
                if (treeNode.right != null) {
                    queue.addLast(treeNode.right);
                }
            }
            flag = !flag;
            result.add(level);

        }
        return result;
    }
}
```
## （三）  二叉搜索树应用的题（特殊的二叉树的数据结构）

#### validate-binary-search-tree

[validate-binary-search-tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。

思路 1：中序遍历，如果中序遍历得到的节点的值小于等于前一个 preVal，说明不是二叉搜索树

思路 2：递归法，判断左 MAX < 根 < 右 MIN
##### 中序遍历
```java
class Solution {
   //全局
    long pre = Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        //递归终止条件
        if (root == null) {
            return true;
        }
        //中序遍历的一般情况
        // 先访问左子树
        if (!isValidBST(root.left)) {
            return false;
        }
        // 访问当前节点：如果当前节点小于等于中序遍历的前一个节点，说明不满足BST，返回 false；否则继续遍历。
        if (root.val <= pre) {
            return false;
        }
        pre = root.val;
        // 访问右子树
        return isValidBST(root.right);
    }
}
```

##### 递归

```java
// 递归
class Solution {
    public boolean isValidBST(TreeNode root) {
        if(root==null){
            return true;
        }
        if(root.left==null&&root.right==null){
            return true;
        }
       

        if(root.left!=null&&root.right!=null){
             if(root.left.val<root.val&&root.val<root.right.val){
            boolean left=isValidBST(root.left);
            boolean right=isValidBST(root.right);
            //左子树最大的(一直往右)比root小
            TreeNode leftmax=root.left;
            while(leftmax.right!=null){
                leftmax=leftmax.right;
            }
            //右子树最小的（一直往左）比root大
            TreeNode rightmin=root.right;
            while(rightmin.left!=null){
                rightmin=rightmin.left;
            }
            boolean all;
            if((leftmax.val<root.val)&&(root.val<rightmin.val)){
                all=true;
            }else{
                all=false;
            }
        
            return left&&right&&all;
            }else{
                return false;
            }
        }else if(root.right != null&&root.left==null){
           if(root.val<root.right.val){
            boolean right=isValidBST(root.right);
           
            //右子树最小的（一直往左）比root大
            TreeNode rightmin=root.right;
            while(rightmin.left!=null){
                rightmin=rightmin.left;
            }
            boolean all;
            if(root.val<rightmin.val){
                all=true;
            }else{
                all=false;
            }
        
            return right&&all;
            }else{
                 return false;
         }  
        }else{
            if(root.val>root.left.val){
            boolean left=isValidBST(root.left);
           
            //右子树最小的（一直往左）比root大
            TreeNode leftmax=root.left;
            while(leftmax.right!=null){
                leftmax=leftmax.right;
            }
            boolean all;
            if(leftmax.val<root.val){
                all=true;
            }else{
                all=false;
            }
        
            return left&&all;
            }else{
                 return false;
         } 
        }

    }
}
```

#### insert-into-a-binary-search-tree

[insert-into-a-binary-search-tree](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

> 给定二叉搜索树（BST）的根节点和要插入树中的值，将值插入二叉搜索树。 返回插入后二叉搜索树的根节点。

思路：找到最后一个叶子节点满足插入条件即可

```java
// DFS查找插入位置
class Solution {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
        if (root.val < val) {
            root.right = insertIntoBST(root.right, val);
        } else {
            root.left = insertIntoBST(root.left, val);
        }
        return root;
    }
}
```

## 总结

- 掌握二叉树递归与非递归遍历
- 理解 DFS 前序遍历-->引出分治法
- 理解 BFS 层次遍历



