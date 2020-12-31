# 堆和优先队列
## 一 [基础知识](https://www.yuque.com/liweiwei1419/algo/gvssf9)
### 队列与优先队列
* 「队列」是一种先进先出（FIFO）的数据结构，出队顺序谁先来谁先出去。
* 「优先队列」是出队顺序与入队顺序无关，只与队列中元素的优先级有关，优先级最高的元素最先出队。
### 三种数据结构对于实现优先队列的时间复杂度的比较
「优先队列」是更抽象的数据结构

| 使用什么数据结构来实现优先队列 | 入队操作 | 出队操作 |
| ------------------------------ | -------- | -------- |
| 普通数组                       | O(1)     | O(n)     |
| 顺序数组                       | O(n)     | O(1)     |
| 堆（就是优先队列）             | O(log n) | O(log n) |

为平衡了优先队列入队和出队这两个操作的时间复杂度，产生的数据结构就是**堆**。
### 堆的基本实现：二叉堆
最大堆两个的性质
* 「最大堆」是一棵「完全二叉树」
	
	>完全二叉树：从形状上看，除了最后一层之外，其它层结点的数量达到最大值，并且最后一层的结点全部集中在左侧
	
* 堆有序

  > 任意一个结点，如果有孩子结点的话，孩子结点的值一定不会大于父亲结点的值
### 使用数组来实现二叉堆
* 数组下标从1开始

  * 从子结点下标找到父结点下标：parent=i/2（注意这里不能整除的时候需要向下取整）
  * 从父节点下标找到两个子结点下标：left child=2*i,right child=2\*i+1。
* 数组下标从0开始
  * 从子结点下标找到父结点下标：parent=i-1/2（注意这里不能整除的时候需要向下取整）
  * 从父节点下标找到两个子结点下标：left child=2*i+1,right child=2\*i+2。
```java
// 我们这个版本的实现中，0 号下标是不存数据的，这一点一定要注意
public class MaxHeap {

    private int[] data;
    // 当前堆中存储的元素的个数
    private int count;
    
    // 堆中能够存储的元素的最大数量（为简化问题，不考虑动态扩展）
    private int capacity;

    // 初始化最大堆
    public MaxHeap(int capacity) {
        // 初始化底层数组元素（ 0 号下标位置不存数据，这是为了使得通过父结点获得左右孩子有更好的表达式）
        data = new int[capacity + 1];
        count = 0;
    }

    // 返回堆中的元素个数
    public int size() {
        return count;
    }
    
    // 返回一个布尔值，返回堆中是否为空
    public boolean isEmpty() {
        return count == 0;
    }
}
```

### 最大堆的两个重要操作 (数组下标从0开始)

#### 1）向一个「最大堆」中添加元素（利用上滤siftUp操作）

* 新加的元素放在数组的末尾**（维护完全二叉树）**；

* 新加入的元素调整元素的位置（**siftUp**）：只与父结点比较（不必与兄弟孩子比较），如果比父结点大，就往上交换位置，直到**堆顶 (边界条件)**。**(维护堆有序)**

  ```java
  public void insert(int item) {
      assert count + 1 <= capacity;
      // 把新添加的元素放在数组的最后一位，对应于最大堆最后一个叶子结点
      data[count + 1] = item;
      count++;
      // 考虑将它上移
      siftUp(count);
  }
  ```


  **核心上滤操作**

  ```java
  private void siftUp(int k) {
      // 有下标就要考虑下标越界的情况，已经在下标 1 的位置，就没有必要上移了
      while (k > 1 && data[k / 2] < data[k]) {
          swap(data, k / 2, k);
          k /= 2;
      }
  }
  
  private void swap(int[] data, int index1, int index2) {
      if (index1 == index2) {
          return;
      }
      int temp = data[index1];
      data[index1] = data[index2];
      data[index2] = temp;
  }
  ```

 #### 2）从一个「最大堆」中取出元素(利用下滤siftDown)

- 根结点是堆（数组）中最大的元素，即数组里下标为 1 的元素，所以从最大堆中取出根结点元素（**堆有序的性质**）
- 根结点取出以后，1 号下标位置为空，于是我们将当前数组的最后一个元素放到 1 号下标的位置。这样做是**因为交换和移动的次数最少**，这种想法也应该是十分自然的，（**保持了完全二叉树的性质**）；
- 但是此时数组并不满足最大堆的性质，我们就要进行 **siftDown** 的操作使这个数组保持最大堆的性质。

```java
public int extract_max() {
    assert count > 0:
    max = data[1]
    swap(data,1,count)
    count--;
    shiftDown(1);
    return max
}
```

**核心下滤操作**

`siftDown` 的具体操作步骤：

- 从 1 号下标开始，如果存在右孩子，就把右孩子和左孩子比较，比出较大的那个，再和自己比较，如果比自己大，就交换位置，这样的过程直到「不小于两个孩子结点中的最大者」。

```java
private void shiftDown(int k) {
    // 只要它有孩子，注意，这里的等于号是十分关键的
    while (2 * k <= count) {
        int left = 2 * k;
        //左右孩子中跳出最大的代表 left指向
        if (left + 1 <= count && data[left + 1] > data[left]) {
            // 右边的孩子胜出，右孩子的值比左孩子大
            left++;
        }
        // 如果当前的元素的值，比孩子结点的值要大，则逐渐下落的过程到此结束
        if (data[k] >= data[left]) {
            break;
        }
        // 否则，交换位置
        swap(data,k,left);
        //只要交换了就一直向下过滤
        k = left;
    }
}
```

### [堆排序-升序（数组下标从0开始）](https://github.com/xinguohua/notes/blob/master/docs/LeetCode/basic_algorithm/sort.md)

* 1 构造初始堆，将给定无序序列构造成一个最大堆

  从第一个非叶子结点开始，每个子树进行**下滤**构成1个最大堆
* 2 将堆顶元素与末尾元素进行交换，使末尾元素最大（固定最后元素）然后继续调整堆**（下滤）**
### PriorityQueue--优先队列的JAVA实现

Java中PriorityQueue通过二叉**小顶堆**（升序）实现的优先队列，可以用一棵完全二叉树表示。

这个优先队列中的元素可以默认**自然排序**或者通过提供的[Comparator](https://zhuanlan.zhihu.com/p/54004622)（**比较器**）在队列实例化的时排序

#### [API方法介绍](https://www.cnblogs.com/CarpenterLee/p/5488070.html)

|          | 向优先队列中插入元素（插入后自动调整满足堆顺序） | 堆顶元素   | 优先队列出队（堆顶） | 删除队列中某个元素 |
| -------- | ------------------------------------------------ | ---------- | -------------------- | ------------------ |
| 抛异常   | **add(E e)**                                     | element()  | **remove()**         | remove(Object o)   |
| 不抛异常 | offer(E e)                                       | **peek()** | poll()               |                    |

#### [两种排序方式](https://blog.csdn.net/u010623927/article/details/87179364)

* 自然排序(小顶堆 默认升序)

  ```java
  Queue<Integer> integerPriorityQueue = ``new` `PriorityQueue<>(``7``)
  ```

* 比较器

   ```java
  Queue<Customer> customerPriorityQueue = new PriorityQueue<>(7, idComparator)
  //匿名Comparator实现
  public static Comparator<Customer> idComparator = new Comparator<Customer>(){
          @Override
          public int compare(Customer c1, Customer c2) {
              return (int) (c1.getId() - c2.getId());
  		}
  };
  ```

  


## 二 优先队列应用
[347. 前 K 个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

>给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer, Integer> hashmap = new HashMap<>();
        //将num数组转换为数字-频次的数据结构
        for (int i = 0; i < nums.length; i++) {
            if (hashmap.containsKey(nums[i])){
                hashmap.put(nums[i],hashmap.get(nums[i])+1);
            }else{
                hashmap.put(nums[i],1);
            }
        }
        //构建优先队列，对象为 [数，频次] 以频次降序排列，也就是大顶堆
        PriorityQueue<int[]> queue = new PriorityQueue<>(new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o2[1] - o1[1];
            }
        });

        Set<Integer> keySet = hashmap.keySet();
        for (int key : keySet) {
            queue.add(new int[]{key,hashmap.get(key)});
        }
        int[] result=new int[k];
        for (int i = 0; i <k ; i++) {
            result[i]=queue.remove()[0];
        }
        return result;
    }
}
```



[5638. 吃苹果的最大数目](https://leetcode-cn.com/problems/maximum-number-of-eaten-apples/)

```java
class Solution {
        public int eatenApples(int[] apples, int[] days) {

            int n = apples.length;
            //维护一个优先队列，每次都把当前的苹果数目和时间放进去，按照过期时间建立最小堆
            //每次从堆顶取出,如果时间过期或者个数为0则弹出
            Queue<int[]> q = new PriorityQueue<int[]>(n, new Comparator<int[]>() 			{
                @Override
                public int compare(int[] o1, int[] o2) {
                    return o1[1]-o2[1];
                }
            });
            int i =0;
            int eaten =0;
            while(i<n||q.size()!=0){
                //默认存进去就按腐败时间升序排序
                if(i<n)q.add(new int[]{apples[i], i+days[i]});
                while(q.size()!=0&&(q.peek()[0]==0||q.peek()[1]<=i))q.remove();
                if(q.size()==0){
                    if(i<n){
                        i++;
                        continue;
                    }
                    else return eaten;
                }
                q.peek()[0]--;
                eaten++;
                i++;
            }
            return 0;
        }
    }
```

## 相关题目

[1046. 最后一块石头的重量](https://leetcode-cn.com/problems/last-stone-weight/comments/)