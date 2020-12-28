# 二进制

## 常见二进制操作
### 位运算概览
| 符号 | 描述 | 运算规则                                                     |
| :--- | :--- | :----------------------------------------------------------- |
| &    | 与   | 两个位都为1时，结果才为1                                     |
| \|   | 或   | 两个位都为0时，结果才为0                                     |
| ^    | 异或 | 两个位相同为0，相异为1                                       |
| ~    | 取反 | 0变1，1变0                                                   |
### 移位运算
| 符号 | 描述 | 运算规则                                                     |
| :--- | :--- | :----------------------------------------------------------- |
| <<   | 算数左移 | 为算术左移，各二进位全部左移若干位，高位丢弃，低位补0，相当于乘以2的n次 |
| >>   | 带符号右移 | 为算术右移，各二进位全部右移若干位，**高位补符号位** ，相当于除以2的n次|
| >>>   | 无符号右移 | 为逻辑右移，不论负数与否，结果都是**高位补零**，低位丢弃。|
### 基本操作
* 异或的技巧1：可以将三个数中**重复的两个数去除**，只留下另一个数。eg,1^1^2 = 2。

	>a=0^a=a^0 (0异或任何数都是本身)
	>
	>0=a^a
	>由上面两个推导出：a=a^b^b
* 异或的技巧2：将一个**数的位级表示翻转**。

	>x ^ 1s = ~x
* 异或的技巧3：**交换两个数**a，b。

	>a=a^b
	
	>b=a^b
	
	>a=a^b

### 移除最后一个 1

a=n&(n-1)

### 获取最后一个 1

diff=(n&(n-1))^n

### mask计算
* 获取mask
	
	要获取 111111111，将 0 取反即可，\~0。
	
	要得到只有第 i 位为 1 的 mask，将 1 向左移动 i-1 位即可，1\<\<(i-1) 。例如 1\<\<4 得到只有第 5 位为 1 的 mask ：00010000。
	
	要得到 1 到 i 位为 1 的 mask，(1\<\<i)-1 即可，例如将 (1\<\<4)-1 = 00010000-1 = 00001111。
	
	要得到 1 到 i 位为 0 的 mask，只需将 1 到 i 位为 1 的 mask 取反，即 \~((1\<\<i)-1)。
* mask的&操作

	利用 x & 0s = 0 和 x & 1s = x 的特点，可以实现掩码操作。一个数 num 与 mask：00111100 进行位与操作，只保留 num 中与 mask 的 1 部分相对应的位。

	```
	01011011 &
	00111100
	--------
	00011000
	```
* mask的|操作
	利用 x | 0s = x 和 x | 1s = 1s 的特点，可以实现设值操作。一个数 num 与 mask：00111100 进行位或操作，将 num 中与 mask 的 1 部分相对应的位都设置为 1。

	```
	01011011 |
	00111100
	--------
	01111111
	```

[single-number](https://leetcode-cn.com/problems/single-number/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

思路：利用异或技巧1，a=a^b^b,去除重复的数

```java
class Solution {
    public int singleNumber(int[] nums) {
        int result = 0; //0异或任何数都是本身
        for (int num : nums) {
			//异或技巧1
            // 两个相同的数做异或操作等于0，0与任何数异或，数值不变。
            result = result ^ num;
        }
        return result;
    }
}
```

[single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)

> 给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现了三次。找出那个只出现了一次的元素。

![](http://wardseptember.club/20200622211535.png)

```java
class Solution {
    public int singleNumber(int[] nums) {
        int seenOnce = 0, seenTwice = 0;
        for (int num : nums) {
            // 只出现一次的数按照下面的规则运算后，seenOnce就等于该数。出现三次的数运算后，seenOnce 和 seenTwice都为0
            seenOnce = ~seenTwice & (seenOnce ^ num);
            seenTwice = ~seenOnce & (seenTwice ^ num);
        }
        return seenOnce;
    }
}
```

[single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)

> 给定一个整数数组  `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。
详解见[https://leetcode-cn.com/problems/single-number-iii/solution/zhi-chu-xian-yi-ci-de-shu-zi-iii-by-leetcode/](https://leetcode-cn.com/problems/single-number-iii/solution/zhi-chu-xian-yi-ci-de-shu-zi-iii-by-leetcode/)
```java
class Solution {
    public int[] singleNumber(int[] nums) {
        int bitmask = 0;
        // 这个循环只会保留出现一次的数，假设两个出现一次的数为x、y, x和y都被保存在bitmask里面，下面把他们分离出来
        for (int num : nums) {
            bitmask ^= num;
        }
        // 保留二进制数，最右边为1的值，其他为都为0，这个1要么是x的要么是y的
        int diff = bitmask & (-bitmask);
        int x = 0;
        // 这个循环确定diff是x的还是y的，并保存起来
        for (int num : nums) {
            if ((diff & num) != 0) {
                x ^= num;
            }
        }
        // y = bitmask ^ x;
        return new int[]{x, bitmask ^ x};
    }
}
```

[number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)

> 编写一个函数，输入是一个无符号整数，返回其二进制表达式中数字位数为 ‘1’  的个数（也被称为[汉明重量](https://baike.baidu.com/item/%E6%B1%89%E6%98%8E%E9%87%8D%E9%87%8F)）。

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int sum = 0;
        while (n != 0) {
            sum++;
            // n & (n - 1)运算，将n的二进制数最后一位1变为0，循环直到n为0
            n = n & (n - 1);
        }
        return sum;
    }
}
```

[counting-bits](https://leetcode-cn.com/problems/counting-bits/)

> 给定一个非负整数  **num**。对于  0 ≤ i ≤ num  范围中的每个数字  i ，计算其二进制数中的 1 的数目并将它们作为数组返回。


```java
class Solution {
    public int[] countBits(int num) {
        int[] res = new int[num + 1];
        for (int i = 0; i <= num; i++) {
            res[i] = count(i);
        }
        return res;
    }
    
    private int count(int num) {
        int sum = 0;
        while (num != 0) {
            sum++;
            num &= num - 1;
        }
        return sum;
    }
}
```

另外一种动态规划解法

```java
class Solution {
    public int[] countBits(int num) {
        int[] res = new int[num + 1];
        for (int i = 1; i <= num; i++) {
            // 上一个缺1的元素+1即可
            res[i] = res[i & (i - 1)] + 1;
        }
        return res;
    }
}
```

[reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)

> 颠倒给定的 32 位无符号整数的二进制位。

思路：依次颠倒即可

取当前 n 的最后一位：n & 1
将最后一位移动到对应位置，第一次为 31 位，第二次是 30 位，即：31、30、29... 1、0，写作代码 bit << 31;
退出条件，二进制反转时，如果剩余位数全位 0，则可以不用再反转。
```java
public class Solution {
    // you need treat n as an unsigned value
    public int reverseBits(int n) {
        int ans = 0;
        for (int bitsize = 31; n != 0; n = n >>> 1, bitsize--) {
            ans += (n & 1) << bitsize;
        }
        return ans;
    }
}
```

[bitwise-and-of-numbers-range](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/)

> 给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。

详解见[https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/solution/shu-zi-fan-wei-an-wei-yu-by-leetcode/](https://leetcode-cn.com/problems/bitwise-and-of-numbers-range/solution/shu-zi-fan-wei-an-wei-yu-by-leetcode/)
```java
class Solution {
    public int rangeBitwiseAnd(int m, int n) {
        while (m < n) {
            n &= n - 1;
        }
        return m & n;
    }
}
```


- [ ] [single-number-ii](https://leetcode-cn.com/problems/single-number-ii/)
- [ ] [single-number-iii](https://leetcode-cn.com/problems/single-number-iii/)
- [ ] [number-of-1-bits](https://leetcode-cn.com/problems/number-of-1-bits/)
- [ ] [counting-bits](https://leetcode-cn.com/problems/counting-bits/)
- [ ] [reverse-bits](https://leetcode-cn.com/problems/reverse-bits/)
