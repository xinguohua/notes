# 数组

## [技巧 List与Array之间的转换](https://blog.csdn.net/zjx2016/article/details/78273192)

### （一）List转数组

* 使用for循环

  ```java
  //要转换的list集合
  List testList = new ArrayList(){{add(“aa”);add(“bb”);add(“cc”);}};
   //初始化需要得到的数组
  String[] array = new String[testList.size()];
  
  //使用for循环得到数组
  for(int i = 0; i < testList.size();i++){
      array[i] = testList.get(i);
  }
  
  //打印数组
  for(int i = 0; i < array.length; i++){
      System.out.println(array[i]);
  }
  ```

* **使用toArray()方法(*)** 

  `列表.toArray`里放要转换的数组
  
  ```java
  //要转换的list集合
  List<String> testList = new ArrayList<String>({{add("aa");add("bb");add("cc");}};
  
  //使用toArray(T[] a)方法
  String[] array2 = testList.toArray(new String[testList.size()]);
  
  //打印该数组
  for(int i = 0; i < array2.length; i++){
      System.out.println(array2[i]);
  }
  ```

### （二）数组转List

* 使用for循环

  ```java
  //需要转换的数组
  String[] arrays = new String[]{"aa","bb","cc"};
  
  //初始化list
  List<String> list = new ArrayList<String>();
  
  //使用for循环转换为list
  for(String str : arrays){
      list.add(str);
  }
  
  //打印得到的list
  System.out.println(list);
  ```

* **使用asList（）***

  利用列表构造方法，使用Arrays.asList(数组)

  ```java
  ArrayList<String> arrayList = new ArrayList<String>(Arrays.asList(arrays));
  ```




# 题目

## （一）数组之指针类型

#### [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

>给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

一开始两个指针一个指向开头一个指向结尾，此时容器的底是最大的，接下来随着指针向内移动，会造成容器的底变小，在这种情况下想要让容器盛水变多，就只有在容器的高上下功夫。 那我们该如何决策哪个指针移动呢？我们能够发现不管是左指针向右移动一位，还是右指针向左移动一位，容器的底都是一样的，都比原来减少了 1。这种情况下我们想要让指针移动后的容器面积增大，就要使移动后的容器的高尽量大，所以我们选择指针所指的高较小的那个指针进行移动，这样我们就保留了容器较高的那条边，放弃了较小的那条边，以获得有更高的边的机会。

```java
class Solution {
    //双指针
    public int maxArea(int[] height) {
        int left=0,right=height.length-1;
        int max=0;
        while(left<=right){
            int temp=Math.min(height[left],height[right])*(right-left);
            if(temp>max){
                max=temp;
            }
            if(height[left]<=height[right]){
                left++;
            }else{
                right--;
            }
        }
        return max;
    }
}
```
#### [75. 颜色分类](https://leetcode-cn.com/problems/sort-colors/)
三指针 
- 两个待定区间的指针
- 一个循环指针

三个区间
- all in [0, zero) = 0     zero指针指的为待0区间下一个可能的元素
- all in [zero, i) = 1
- **注意待定区间循环还未访问到的区间 [i,two)**
- all in [two, len - 1] = 2    two是区间里的元素 2区间下一个元素two--

```java
public class Solution {

    public void sortColors(int[] nums) {
        int len = nums.length;
        if (len < 2) {
            return;
        }

        // all in [0, zero) = 0     zero指针指的为待0区间下一个可能的元素
        // all in [zero, i) = 1
        // all in [two, len - 1] = 2    two是区间里的元素 2区间下一个元素two--
        
        // 循环终止条件是 i == two，那么循环可以继续的条件是 i < two
        // 为了保证初始化的时候 [0, zero) 为空，设置 zero = 0，
        // 所以下面遍历到 0 的时候，先交换，再加
        int zero = 0;

        // 为了保证初始化的时候 [two, len - 1] 为空，设置 two = len
        // 所以下面遍历到 2 的时候，先减，再交换
        int two = len;

        //循环变量
        int i = 0;
        // 当 i == two 上面的三个子区间正好覆盖了全部数组
        // 因此，循环可以继续的条件是 i < two
        while (i < two) {
            if (nums[i] == 0) {
                swap(nums, i, zero);
                zero++;
                i++;
            } else if (nums[i] == 1) {
                i++;
            } else {
                two--;
                swap(nums, i, two);
            }
        }
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```
## （二）数组之索引类型

#### [41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/)

> 给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数。

数组索引的利用

遍历一次数组把大于等于1的和小于数组大小的值放到原数组对应位置，然后再遍历一次数组查当前下标是否和值对应，如果不对应那这个下标就是答案，否则遍历完都没出现那么答案就是数组长度加1。

```java
class Solution {
    public static int firstMissingPositive(int[] nums) {
        //遍历一次数组把大于等于1的和小于数组大小的值放到原数组对应位置
        for(int i=0;i<nums.length;i++){
            while(nums[i]>=1&&nums[i]<=nums.length&&nums[i]!=i+1){
             //如果要交换的地方---其存放的数值和索引不一样则交换
              if (nums[nums[i]-1]!=nums[i]){
                  swap(nums,i,nums[i]-1);
              }else{
                  //否则没必要交换
                  break;
              }

            }
        }
        //再遍历一次数组查当前下标是否和值对应，如果不对应那这个下标就是答案
        for(int i=0;i<nums.length;i++){
            if(nums[i]!=i+1){
                return i+1;
            }
        }
        //否则遍历完都没出现那么答案就是数组长度加1。
        return nums.length+1;
    }

    public static void swap(int[] nums,int i,int j){
        int temp=nums[i];
        nums[i]=nums[j];
        nums[j]=temp;
    }

}
```



## 数组之排序，列表转换技巧类型

#### [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

>给出一个区间的集合，请合并所有重叠的区间。

* 运用技巧数组排序
* 数组和List相互转换

```java
class Solution {
    public int[][] merge(int[][] intervals) {
        //按数组下标升序排序intervals
        Arrays.sort(intervals, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                //o1[0]<o2[0] 不不交换 升序
                return o1[0]-o2[0];
            }
        });


        //结果存放在ArrayList<int[]>
        LinkedList<int[]> result= new LinkedList<>();
        result.addLast(intervals[0]);

        //遍历intervals依次两两比较（result结果与intervals比较）
        for (int i = 1; i < intervals.length; i++) {
            int[] compare = result.peekLast();
            //情况1：互不相交
            if (compare[1]<intervals[i][0]){
                result.addLast(intervals[i]);
            }else{
                //情况2：一种相交
                result.removeLast();
                intervals[i][0]=compare[0];
                intervals[i][1]=Math.max(intervals[i][1],compare[1]);
                result.addLast(intervals[i]);
            }
        }
        

        //List-->Array
        int[][] resultarray = result.toArray(new int[result.size()][2]);
        return  resultarray;
    }
}
```







