# 回溯法

## 背景

### 定义
回溯法（backtrack）常用于遍历列表所有子集，是 DFS 深度搜索一种，一般用于全排列，穷尽所有可能，遍历的过程实际上是一个决策树的遍历过程。时间复杂度一般 O(N!)，它不像动态规划存在重叠子问题可以优化，回溯算法就是纯暴力穷举，复杂度一般都很高。

### DFS和回溯算法区别:

DFS 是一个劲的往某一个方向搜索，而回溯算法建立在 DFS 基础之上的，但不同的是在搜索过程中，达到结束条件后，恢复状态，回溯上一层，再次搜索。因此回溯算法与 DFS 的区别就是有无状态重置

### 何时使用回溯算法
当问题需要 "回头"，以此来查找出所有的解的时候，使用回溯算法。即满足结束条件或者发现不是正确路径的时候(走不通)，要撤销选择，回退到上一个状态，继续尝试，直到找出所有解为止


### 回溯问题的类型

https://leetcode-cn.com/problems/subsets/solution/c-zong-jie-liao-hui-su-wen-ti-lei-xing-dai-ni-gao-/

### 怎么样写回溯算法(从上而下，※代表难点，根据题目而变化)
①画出递归树，找到状态变量(回溯函数的参数)，这一步非常重要※

②根据题意，确立结束条件

③找准选择列表(与函数参数中的状态变量相关),与第一步紧密关联※

④判断是否需要剪枝

⑤作出选择，递归调用，进入下一层

⑥撤销选择



## 模板

```
//全局变量存放结果
int[] result = new int[size];
//1 确定状态变量
void backtrack(选择列表,路径): {
	//2 结束条件
    if 满足结束条件: {
        result.add(路径)
        return
    }
    //3 选择列表
    for (选择 : 选择列表): {
    	剪枝判断 //4
        做选择 //5
        backtrack(选择列表,路径) //6
        撤销选择  //7
    }
}
```


核心就是从选择列表里做一个选择，然后一直递归往下搜索答案，如果遇到路径不通，就返回来撤销这次选择。

# 子集组合类

## 子集类

### [subsets](https://leetcode-cn.com/problems/subsets/)

> 给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

遍历过程

```java
class Solution {
	//全局变量存放：所有路径的集合
    LinkedList<List<Integer>> results = new LinkedList<>();

    public List<List<Integer>> subsets(int[] nums) {

        //记录走过的路径指针
        LinkedList<Integer> pathCur = new LinkedList<>();
		//1 画出递归树，找到状态变量作为参数
        //参数 查找的数组，路径，状态变量
        backtrack(nums, pathCur, 0);


        return results;
    }

    //pathCur为路径指针，要把每一条 path 加入结果集
    private void backtrack(int[] nums, LinkedList<Integer> pathCur, int start) {

        //2 结束条件
        /*
        不存在结束条件。或者说当 start 参数越过数组边界的时候，程序就自己跳过下一层递归了
         */
        results.add(new ArrayList<>(pathCur));
        //3 找选择列表，是上一条选择路径之后的数 start及以后
        for (int i = start; i < nums.length; i++) {
            //4 做出选择
            pathCur.addLast(nums[i]);
            //5 递归进入下一层
            backtrack(nums, pathCur, i + 1);
            //6 撤销选择
            pathCur.removeLast();
        }
    }
}
```


### [subsets-ii](https://leetcode-cn.com/problems/subsets-ii/)

> 给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。说明：解集不能包含重复的子集。

增加了减枝操作
```java
class Solution {
    //全局变量存放结果
    List<List<Integer>> results=new LinkedList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        //路径指针
        LinkedList<Integer> PathCur = new LinkedList<>();
        //排序,将相同元素聚拢，为剪枝提供条件.
        Arrays.sort(nums);
        //1 画出递归树，找到状态变量作为参数
        backtrack(nums,PathCur,0);


        return  results;
    }

    private void backtrack(int[] nums, LinkedList<Integer> pathCur, int start) {
        //2 确定结束条件，本题无结束条件，DFS每一个节点
        results.add(new LinkedList<Integer>(pathCur));

        //3 选择列表
        for (int i = start; i < nums.length; i++) {

            //4 剪枝，相同层, 如果当前元素与上一个元素相同, 则跳过不遍历以实现剪枝.
            if((i-1)>=start && nums[i-1] == nums[i])
                continue;
            //5 做出选择
            pathCur.addLast(nums[i]);
            //6 调用回溯
            backtrack(nums,pathCur,i+1);
            // 7撤销选择
            pathCur.removeLast();
        }

    }
}
```
## 组合类

### [77. 组合](https://leetcode-cn.com/problems/combinations/)

> 给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

backtrak()增加了终止条件`if(level==stop)`，和终止状态参数`level`以及全局变量的终止条件`stop`
```java
class Solution {
   List<List<Integer>> result= new LinkedList<>();
    int stop;
    public List<List<Integer>> combine(int n, int k) {
        stop=k;
        LinkedList<Integer> pathCur = new LinkedList<>();
        //1 回溯函数参数 搜索范围,路径指针，状态变量，层数（终止变量）
        backtrak(n,pathCur,1,0);

        return result;
    }

    private void backtrak(int n, LinkedList<Integer> pathCur, int start, int level) {
        //2 终止条件
        if (level==stop){
            result.add(new LinkedList<>(pathCur));
            return;
        }

        //3 状态参数得选择列表
        for (int i = start; i <=n; i++) {
            //4 剪枝条件 停止所需得数量比还剩下的数量多 则跳过
            if (stop-level>n-i+1){
                continue;
            }
            //4 做出选择
            pathCur.addLast(i);
            //5 调用回溯
            backtrak(n,pathCur,i+1,++level);
            //6 撤销选择
            --level;
            pathCur.removeLast();
        }
    }
}
```
### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

> 给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合

backtrak()中start（状态变量）来控制for循环的起始位置，递归过程start不用i+1因为可以重复调用
```java
class Solution {
   List<List<Integer>> results= new LinkedList<>();
    int target;
    public List<List<Integer>> combinationSum(int[] candidates, int target) {

        this.target=target;
        LinkedList<Integer> pathCur= new LinkedList<>();
        //减枝之前必排序
        Arrays.sort(candidates);
        //1 参数 搜索数组，路径，状态变量start，终止参数sum
        backtack(candidates,pathCur,0,0);

        return results;
    }

    private void backtack(int[] candidates, LinkedList<Integer> pathCur,int start, int sum) {
        //2 终止条件
        if (sum==target){
            results.add(new LinkedList<>(pathCur));
            return;
        }

        //3 选择列表
        for (int i = start; i <candidates.length ; i++) {
            //4 剪枝
            if (candidates[i]>target-sum){
                break;
            }
            //5 做出选择
            pathCur.addLast(candidates[i]);
            sum=sum+candidates[i];
            //6 递归调用
            backtack(candidates,pathCur,i,sum);
            //7 撤销选择
            pathCur.removeLast();
            sum=sum-candidates[i];

        }

    }

}
```
### [40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

> 给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
```java
class Solution {
    //全局变量结果
   List<List<Integer>> results=new LinkedList<>();
   //终止
    int target;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        this.target=target;
        LinkedList<Integer> path=new LinkedList<>();
        //0 排序
        Arrays.sort(candidates);
        //1 参数 搜索数组，路径，状态 ，终止
        backtrack(candidates,path,0,0);

        return results;

    }

    private void backtrack(int[] candidates, LinkedList<Integer> path, int start, int sum) {
        //2 终止条件
        if (sum==target){
            results.add(new LinkedList<>(path));
            return;
        }
        //3 选择列表
        for (int i = start; i < candidates.length; i++) {
            //避免重复
            if (i-1>=start&&candidates[i]==candidates[i-1]){
                continue;
            }
            //剪枝
            if (candidates[i]>target-sum){
                break;
            }
            //4 做出选择
            path.addLast(candidates[i]);
            sum=sum+candidates[i];
            //5 回溯调用
            backtrack(candidates,path,i+1,sum);
            //6 撤销选择
            path.removeLast();
            sum=sum-candidates[i];
        }

    }
}
```
# 排列类

### [permutations](https://leetcode-cn.com/problems/permutations/)

> 给定一个   没有重复   数字的序列，返回其所有可能的全排列。

思路：需要记录已经选择过的元素，满足条件的结果才进行返回

```java
class Solution {
List<List<Integer>> result= new LinkedList<>();
    public List<List<Integer>> permute(int[] nums) {

        LinkedList<Integer> pathCur = new LinkedList<>();
       //构造状态变量
        boolean[] visited = new boolean[nums.length];
        //1 参数 搜索数组，路径，状态变量,终止变量pathcur隐含
        backtrack(nums,pathCur,visited);
        return result;
    }

    private void backtrack(int[] nums, LinkedList<Integer> pathCur, boolean[] visited) {
        //2 终止条件
        if (pathCur.size()==nums.length){
            result.add(new ArrayList<>(pathCur));
            return;
        }
        //3 选择列表
        for (int i = 0; i < nums.length; i++) {
            if (visited[i]==true){
                continue;
            }
            //4 做出选择
            pathCur.addLast(nums[i]);
            visited[i]=true;
            //回溯，调用下级递归
            backtrack(nums,pathCur,visited);
            //5 撤销选择
            pathCur.removeLast();
            visited[i]=false;
        }

}


```

### [permutations-ii](https://leetcode-cn.com/problems/permutations-ii/)

> 给定一个可包含重复数字的序列，返回所有不重复的全排列。

```java
class Solution {
    List<List<Integer>> result= new LinkedList<>();
    public List<List<Integer>> permuteUnique(int[] nums) {
        LinkedList<Integer> pathCur = new LinkedList<>();
        //剪枝提前准备排序
        Arrays.sort(nums);
        //构造状态变量
        boolean[] visited = new boolean[nums.length];
        //1 参数 搜索数组，路径，状态变量,终止变量pathcur隐含
        backtrack(nums,pathCur,visited);
        return result;
    }

    private void backtrack(int[] nums, LinkedList<Integer> pathCur, boolean[] visited) {
        //2 终止条件
        if (pathCur.size()==nums.length){
            result.add(new ArrayList<>(pathCur));
            return;
        }
        //3 选择列表
        for (int i = 0; i < nums.length; i++) {
            //去重 选择列表中与前一项不相等
            if (i > 0 && nums[i] == nums[i - 1] && !visited[i - 1]) {
                continue;
            }
            //选择列表自己没访问过
            if (visited[i]==true){
                continue;
            }

            //4 做出选择
            pathCur.addLast(nums[i]);
            visited[i]=true;
            //回溯，调用下级递归
            backtrack(nums,pathCur,visited);
            //5 撤销选择
            pathCur.removeLast();
            visited[i]=false;
        }

    }
}
```

# 搜索类

待解决



## 挑战题目

- [ ] [letter-combinations-of-a-phone-number](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)
- [ ] [palindrome-partitioning](https://leetcode-cn.com/problems/palindrome-partitioning/)
- [ ] [restore-ip-addresses](https://leetcode-cn.com/problems/restore-ip-addresses/)

