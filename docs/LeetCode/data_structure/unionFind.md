# 并查集

## 基础知识



# 题目

## 未加速

#### [990. 等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)

>给定一个由表示变量之间关系的字符串方程组成的数组，每个字符串方程 equations[i] 的长度为 4，并采用两种不同的形式之一："a==b" 或 "a!=b"。在这里，a 和 b 是小写字母（不一定不同），表示单字母变量名。

```java
//并查集数据结构
public class UnionFind{
    //数组存放每个节点的父节点
    private int[] parent;

    //初始化，每个节点都是根节点
    //即x==parent[x] 父节点就是本身
    public UnionFind(int n){
        parent=new int[n];
        for(int i=0;i<n;i++){
            parent[i]=i;
        }
    }

    //查找结点的根节点，直到父节点和本身相同
    public int find(int x){
        while(x!=parent[x]){
            int x_Parent=parent[x];
            //下次查找的
            x=x_Parent;
            parent[x]=parent[x_Parent];
            
        }
        return x;
    }

    //连接 x的根节点成为y根节点的子节点
    public void union(int x,int y){
        int rootX=find(x);
        int rootY=find(y);
        parent[rootX]=rootY;
    }

    //根节点是否相同
    public boolean isConnected(int x, int y) {
            return find(x) == find(y);
      }
} 
class Solution {
    public boolean equationsPossible(String[] equations) {
        UnionFind unionFind = new UnionFind(26);
        //扫描等式，建立连接关系union
        for(String equation :equations){
            char[] charArray=equation.toCharArray();
            if(charArray[1]=='='){
                int index1=charArray[0]-'a';
                int index2=charArray[3]-'a';
                unionFind.union(index1,index2);
            }
        }
         //扫描不等式，判断连接关系isConnected
        for(String equation :equations){
            char[] charArray=equation.toCharArray();
            if(charArray[1]=='!'){
                int index1=charArray[0]-'a';
                int index2=charArray[3]-'a';
                
                if(unionFind.isConnected(index1,index2)){
                    return false;
                }
            }
        }
        //所有不等式扫描完成 都符合则返回true
        return true;
    }
}
```



## 加速 路径压缩

#### [1202. 交换字符串中的元素](https://leetcode-cn.com/problems/smallest-string-with-swaps/)

>给你一个字符串 s，以及该字符串中的一些「索引对」数组 pairs，其中 pairs[i] = [a, b] 表示字符串中的两个索引（编号从 0 开始）。
>
>你可以 任意多次交换 在 pairs 中任意一对索引处的字符。
>
>返回在经过若干次交换后，s 可以变成的按字典序最小的字符串
>

```java
public class Solution {
  public String smallestStringWithSwaps(String s, List<List<Integer>> pairs) {
        UnionFind unionFind = new UnionFind(s.length());
        //建立并查集关系union
        for (List<Integer> pair : pairs) {
            int index1 = pair.get(0);
            int index2=pair.get(1);
            unionFind.union(index1,index2);
        }


        //取得对应联通集
        HashMap<Integer,List<Integer>> rootListMap=new HashMap<>();
        for (int i = 0; i < s.length(); i++) {
            int i_root = unionFind.find(i);
            if (rootListMap.containsKey(i_root)){
                List<Integer> list = rootListMap.get(i_root);
                list.add(i);
                rootListMap.put(i_root,list);
            }else{
                ArrayList<Integer> list = new ArrayList<>();
                list.add(i);
                rootListMap.put(i_root,list);
            }
        }
        char[] result=new char[s.length()];
        Set<Integer> rootSet = rootListMap.keySet();
        for (int root : rootSet) {
            List<Integer> indexs = rootListMap.get(root);
            ArrayList<Character> characters = new ArrayList<>();
            for (int index : indexs) {
                characters.add(s.charAt(index));
            }
            Collections.sort(characters);
            int i=0;
            for (int index : indexs) {
                result[index]=characters.get(i++);
            }
        }
        return new String(result);
    }
}
class UnionFind{
    //数组存放每个节点的父节点
    public int[] parent;

    //初始化，每个节点都是根节点
    //即x==parent[x] 父节点就是本身
    public UnionFind(int n){
        parent=new int[n];
        for(int i=0;i<n;i++){
            parent[i]=i;
        }
    }

    //查找结点的根节点，直到父节点和本身相同
    public int find(int x){
        while(x!=parent[x]){
          //实现路径压缩
           parent[x] = parent[parent[x]];
           x = parent[x];

        }
        return x;
    }

    //连接 x的根节点成为y根节点的子节点
    public void union(int x,int y){
        int rootX=find(x);
        int rootY=find(y);
        parent[rootX]=rootY;
    }

    //根节点是否相同
    public boolean isConnected(int x, int y) {
        return find(x) == find(y);
    }
}
```

#### [1722. 执行交换操作后的最小汉明距离](https://leetcode-cn.com/problems/minimize-hamming-distance-after-swap-operations/)

>给你两个整数数组 source 和 target ，长度都是 n 。还有一个数组 allowedSwaps ，其中每个 allowedSwaps[i] = [ai, bi] 表示你可以交换数组 source 中下标为 ai 和 bi（下标从 0 开始）的两个元素。注意，你可以按 任意 顺序 多次 交换一对特定下标指向的元素。
>
>相同长度的两个数组 source 和 target 间的 汉明距离 是元素不同的下标数量。形式上，其值等于满足 source[i] != target[i] （下标从 0 开始）的下标 i（0 <= i <= n-1）的数量。
>
>在对数组 source 执行 任意 数量的交换操作后，返回 source 和 target 间的 最小汉明距离 。

```java


public class Solution {

   public int minimumHammingDistance(int[] source, int[] target, int[][] allowedSwaps) {
        int res = 0;
        int n = source.length;
        UnionFindSet unionFindSet = new UnionFindSet(n);
        for(int[] arr : allowedSwaps)
            unionFindSet.union(arr[0], arr[1]);
        //<当前元素, 当前元素的索引的集合>
        Map<Integer, List<Integer>> map = new HashMap<>();

        //遍历target数组
        for(int i = 0; i < n; i++) {
            //记录target元素以及索引
            //因为可能会出现重复元素所以用List集合
            List<Integer> list = map.getOrDefault(target[i], new LinkedList<>());
            list.add(i);
            map.put(target[i], list);
        }

        //遍历source数组
        for(int i = 0; i < n; i++) {
            int num = source[i];
            //source的数字num在target中找不到
            if(!map.containsKey(num)) {
                res++;
            }else {
                //source的数字num在target中可以找到
                //默认不在在一个连通器内
                boolean flag = false;
                List<Integer> list = map.get(num);
                //遍历num在target中的索引
                for(int j = 0; j < list.size(); j++) {
                    //当前索引
                    int index = list.get(j);
                    //source[i]可以通过若干操作移到与之相等的source[index]
                    if(unionFindSet.isConnected(i, index)) {
                        //移除index
                        list.remove(j);
                        map.put(num, list);
                        //在一个连通器内
                        flag = true;
                        break;
                    }
                }
                //所有的index都尝试了还是false
                //果断+1
                if(!flag)
                    res++;
            }
        }
        return res;
    }
}

//并查集
class UnionFindSet {
    private int[] parent;
    private int n;
    public UnionFindSet(int n) {
        this.n = n;
        this.parent = new int[n];
        //初始化
        //自己和自己联通
        for(int i = 0; i < n; i++)
            parent[i] = i;
    }

    //找到x的根节点
    public int find(int x) {
        if(x == parent[x])
            return x;
        //路径压缩
        parent[x] = find(parent[x]);
        return parent[x];
    }

    public void union(int x, int y) {
        int index1 = find(x), index2 = find(y);
        if(index1 == index2)
            return;
        parent[index1] = index2;
    }

    //判断是否连通
    public boolean isConnected(int x, int y) {
        return find(x) == find(y);
    }
}
```

#### [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/)

>在本问题中, 树指的是一个连通且无环的无向图。
>
>输入一个图，该图由一个有着N个节点 (节点值不重复1, 2, ..., N) 的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。
>
>结果图是一个以边组成的二维数组。每一个边的元素是一对[u, v] ，满足 u < v，表示连接顶点u 和v的无向图的边。
>
>返回一条可以删去的边，使得结果图是一个有着N个节点的树。如果有多个答案，则返回二维数组中最后出现的边。答案边 [u, v] 应满足相同的格式 u < v。
>

```java
public class UninFind{
    //数组存放每个节点的父节点
    private int[] parent;

    //初始化，每个节点都是根节点，互相不连通
    //即x==parent[x] 父节点就是本身节点
    public UninFind(int n){
        parent=new int[n];
        for(int i=0;i<n;i++){
            parent[i]=i;
        }
    }

    //查找节点的根节点，直到父节点（索引和本身相同）
    public int find(int x){
        while(x!=parent[x]){
          //实现路径压缩
           parent[x] = parent[parent[x]];
           x = parent[x];

        }
        return x;
    }

    //连接 x的根节点成为y根节点的子节点
    public void union(int x,int y){
        int rootX=find(x);
        int rootY=find(y);
        parent[rootX]=rootY;
    }

    //根节点是否相同
    public boolean isConnected(int x, int y) {
        return find(x) == find(y);
    }
}
class Solution {
    public int[] findRedundantConnection(int[][] edges) {
        //0不用
        UninFind uninFind=new UninFind(edges.length+1);
        int[] result=new int[2];
        for(int i=0;i<edges.length;i++){
           if(!uninFind.isConnected(edges[i][0],edges[i][1])){
               uninFind.union(edges[i][0],edges[i][1]);
           }else{
               result[0]=edges[i][0];
               result[1]=edges[i][1];
               break;
           } 
            
        }
           return result;
    }
}
```

#### [947. 移除最多的同行或同列石头](https://leetcode-cn.com/problems/most-stones-removed-with-same-row-or-column/)

我们建议从这个问题的「语义」去理解维度。

在消除「最多」个数的石子以后，不同的「行」和「列」最多只能有一个石子保留。因此**行和列的地位是等同的，行号和列号没有先后顺序，行号可以接在列号的后面（或者列表可以接在行号的后面）构成一维数组。**



```java
public class Solution {
    public int removeStones(int[][] stones) {

        UnionFind unionFind = new UnionFind();

        for(int[] stone:stones){
            //区分行号和列号
            unionFind.union(stone[0] + 10001, stone[1]);
        }
        return stones.length-unionFind.getCount();
    }
}

class UnionFind{
    //不确定并查集底层有几个，所以不用数组
    private Map<Integer,Integer> parent;
    //联通分量的个数
    private int count;

    public UnionFind(){
        this.parent=new HashMap<>();
        //联通集个数
        this.count=0;
    }

    public int getCount(){
        return count;
    }

    public int find(int x){
        if(!parent.containsKey(x)){
            parent.put(x,x);
            // 并查集集中新加入一个结点，结点的父亲结点是它自己，所以连通分量的总数 +1
            count++;
        }

        //路径压缩
        while(x!=parent.get(x)){
            //爷爷
            int parent_x=parent.get(parent.get(x));
            x = parent_x;
        }
        
        return parent.get(x);
    }

    //对于每一块石头对横坐标和纵坐标合并
    public void union(int x,int y){
        int rootX =find(x);
        int rootY =find(y);

        if(rootX==rootY){
            return;
        }

        parent.put(rootX,rootY);
        // 两个连通分量合并成为一个，连通分量的总数 -1
        count--;
    }
}
```





#### [803. 打砖块](https://leetcode-cn.com/problems/bricks-falling-when-hit/)

有一个 m x n 的二元网格，其中 1 表示砖块，0 表示空白。砖块 稳定（不会掉落）的前提是：

一块砖直接连接到网格的顶部，或者
至少有一块相邻（4 个方向之一）砖块 稳定 不会掉落时
给你一个数组 hits ，这是需要依次消除砖块的位置。每当消除 hits[i] = (rowi, coli) 位置上的砖块时，对应位置的砖块（若存在）会消失，然后其他的砖块可能因为这一消除操作而掉落。一旦砖块掉落，它会立即从网格中消失（即，它不会落在其他稳定的砖块上）。

返回一个数组 result ，其中 result[i] 表示第 i 次消除操作对应掉落的砖块数目。



```java
public class Solution {

    private int rows;
    private int cols;

    //方向数组
    public static final int[][] DIRECTIONS = {{0, 1}, {1, 0}, {-1, 0}, {0, -1}};

    public int[] hitBricks(int[][] grid, int[][] hits) {
        this.rows = grid.length;
        this.cols = grid[0].length;

        // 第 1 步：把 grid 中的砖头全部击碎，通常算法问题不能修改输入数据，这一步非必需，可以认为是一种答题规范
        int[][] copy = new int[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                copy[i][j] = grid[i][j];
            }
        }

        // 把 copy 中的砖头全部击碎
        for (int[] hit : hits) {
            copy[hit[0]][hit[1]] = 0;
        }

        // 第 2 步：建图，把砖块和砖块的连接关系输入并查集，size 表示二维网格的大小，也表示虚拟的「屋顶」在并查集中的编号
        int size = rows * cols;
        //UninFind数组size存放的就是天花板
        UnionFind unionFind = new UnionFind(size + 1);

        // 将下标为 0 的这一行的砖块与「屋顶」相连
        for (int j = 0; j < cols; j++) {
            if (copy[0][j] == 1) {
                unionFind.union(j, size);
            }
        }

        // 其余网格，如果是砖块向上、向左看一下，如果也是砖块，在并查集中进行合并
        for (int i = 1; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (copy[i][j] == 1) {
                    // 如果上方也是砖块
                    if (copy[i - 1][j] == 1) {
                        unionFind.union(getIndex(i - 1, j), getIndex(i, j));
                    }
                    // 如果左边也是砖块
                    if (j > 0 && copy[i][j - 1] == 1) {
                        unionFind.union(getIndex(i, j - 1), getIndex(i, j));
                    }
                }
            }
        }

        // 第 3 步：按照 hits 的逆序，在 copy 中补回砖块，把每一次因为补回砖块而与屋顶相连的砖块的增量记录到 res 数组中
        int hitsLen = hits.length;
        int[] res = new int[hitsLen];
        for (int i = hitsLen - 1; i >= 0; i--) {
            int x = hits[i][0];
            int y = hits[i][1];

            // 注意：这里不能用 copy，语义上表示，如果原来在 grid 中，这一块是空白，这一步不会产生任何砖块掉落
            // 逆向补回的时候，与屋顶相连的砖块数量也肯定不会增加
            if (grid[x][y] == 0) {
                continue;
            }

            // 补回之前与屋顶相连的砖块数
            int origin = unionFind.getSize(size);

            // 注意：如果补回的这个结点在第 1 行，要告诉并查集它与屋顶相连（逻辑同第 2 步）
            if (x == 0) {
                unionFind.union(y, size);
            }

            // 在 4 个方向上看一下，如果相邻的 4 个方向有砖块，合并它们
            for (int[] direction : DIRECTIONS) {
                int newX = x + direction[0];
                int newY = y + direction[1];

                if (inArea(newX, newY) && copy[newX][newY] == 1) {
                    unionFind.union(getIndex(x, y), getIndex(newX, newY));
                }
            }

            // 补回之后与屋顶相连的砖块数
            int current = unionFind.getSize(size);
            // 减去的 1 是逆向补回的砖块（正向移除的砖块），与 0 比较大小，是因为存在一种情况，添加当前砖块，不会使得与屋顶连接的砖块数更多
            res[i] = Math.max(0, current - origin - 1);

            // 真正补上这个砖块
            copy[x][y] = 1;
        }
        return res;
    }

    /**
     * 输入坐标在二维网格中是否越界
     *
     * @param x
     * @param y
     * @return
     */
    private boolean inArea(int x, int y) {
        return x >= 0 && x < rows && y >= 0 && y < cols;
    }

    /**
     * 二维坐标转换为一维坐标
     *
     * @param x
     * @param y
     * @return
     */
    private int getIndex(int x, int y) {
        return x * cols + y;
    }

    private class UnionFind {

        /**
         * 当前结点的父亲结点
         */
        private int[] parent;
        /**
         * 以当前结点为根结点的子树的结点总数
         */
        private int[] size;

        public UnionFind(int n) {
            parent = new int[n];
            size = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }

        /**
         * 路径压缩，只要求每个不相交集合的「根结点」的子树包含的结点总数数值正确即可，因此在路径压缩的过程中不用维护数组 size
         *
         * @param x
         * @return
         */
        public int find(int x) {
            if (x != parent[x]) {
                parent[x] = find(parent[x]);
            }
            return parent[x];
        }

        public void union(int x, int y) {
            int rootX = find(x);
            int rootY = find(y);

            if (rootX == rootY) {
                return;
            }
            parent[rootX] = rootY;
            // 在合并的时候维护数组 size
            size[rootY] += size[rootX];
        }

        /**
         * @param x
         * @return x 在并查集的根结点的子树包含的结点总数
         */
        public int getSize(int x) {
            int root = find(x);
            return size[root];
        }
    }
}


```

