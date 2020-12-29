# 滑动窗口

## 模板
维持窗口[left,righ)
```java
/* 滑动窗口算法框架 */
class Solution {
    void slidingWindow(string s, string t) {
		//（一）初始化need目标，window窗口
        HashMap<Character, Integer> need, window;
        for (char c : t) {
            need.put(c, need.getOrDefault(c, 0) + 1);
        }
		//（二）初始化窗口的左右指针
        int left = 0, right = 0;
		//窗口中满足need的个数
        int match = 0;
		//（三）右指针在控制窗口
        while (right < s.size()) {

			//（四）右移：获取移入，更新，右移

            // c 是将移入窗口的字符
            char c = s.charAt(right);
 			// 进行窗口内数据的一系列更新
            ...
            // 右移窗口
            right++;

           
    		//此时窗口[left,right)
            /*** debug 输出的位置 ***/
            System.out.print("window: [%d, %d)\n", left, right);
            /********************/
    		
			//（五）左移：判断左移，获取移出，更新，左移

            // 判断左侧窗口是否要收缩
            while (window needs shrink) {
                // d 是将移出窗口的字符
                char d = s.charAt(left);
           
                // 进行窗口内数据的一系列更新
                ...
				// 左移窗口
                left++;
            }
        }
    }
}
```
模板五步
- （一）初始化need目标，window窗口
- （二）初始化窗口的左右指针
- （三）右指针在控制窗口
- （四）右移：获取移入，更新，右移
- （五）左移：判断左移，获取移出，更新，左移


需要变化的地方
- 1、右指针右移之后窗口数据更新
- 2、判断窗口是否要收缩
- 3、左指针左移之后窗口数据更新
- 4、根据题意计算结果

对应四个问题
- 1、当移动 right 扩大窗口，即加入字符时，应该更新哪些数据？
- 2、什么条件下，窗口应该暂停扩大，开始移动 left 缩小窗口？
- 3、当移动 left 缩小窗口，即移出字符时，应该更新哪些数据？
- 4、我们要的结果应该在扩大窗口时还是缩小窗口时进行更新？

## 示例

[minimum-window-substring](https://leetcode-cn.com/problems/minimum-window-substring/)

> 给你一个字符串 S、一个字符串 T，请在字符串 S 里面找出：包含 T 所有字母的最小子串

回答模板四个问题：
- 如果一个字符进入窗口，应该增加 window 计数器和mathch匹配数；
- 当 mathch 满足 need 时应该收缩窗口；
- 如果一个字符将移出窗口的时候，应该减少 window 计数器和mathch匹配数；
- 应该在收缩窗口的时候更新最终结果。

算法流程

1. 我们在字符串 S 中使用双指针中的左右指针技巧，初始化 left = right = 0，把索引左闭右开区间 [left, right) 称为一个「窗口」。
2. 我们先不断地增加 right 指针扩大窗口 [left, right)，直到窗口中的字符串符合要求（包含了 T 中的所有字符）**寻找一个「可行解」**。
3. 此时，我们停止增加 right，转而不断增加 left 指针缩小窗口 [left, right)，直到窗口中的字符串不再符合要求（不包含 T 中的所有字符了）。同时，每次增加 left，我们都要更新一轮结果。**优化这个「可行解」**
4. 重复第 2 和第 3 步，直到 right 到达字符串 S 的尽头。

```java
class Solution {
   public String minWindow(String s, String t) {

        //（一）初始化need目标，window窗口
        // needs存储t的<字符,出现次数>,windows存储<s中与t中字符相同的字符,出现次数>
        HashMap<Character, Integer> needs = new HashMap<>();
        HashMap<Character, Integer> windows = new HashMap<>();
        // 初始化needs
        for (char c : t.toCharArray()) {
            needs.put(c, needs.getOrDefault(c, 0) + 1);
        }

        //（二）初始化窗口的左右指针
        //初始化 滑动窗口的左右指针及滑动窗口的长度【left,right】 match为need和window匹配的字符数
        int left = 0, right = 0, match = 0;
        int sLength = s.length();
        //结果起始位置，结束位置
        int start = 0, minLen = Integer.MAX_VALUE;

        //（三）右指针在控制窗口
        while (right < sLength) {


            //（四）右移：获取移入，更新，右移
            // temp移入窗口的字符
            char temp = s.charAt(right);
            //进行窗口内数据的一系列更新
            // 如果是need中字符，在windows里添加，累计出现次数
            if (needs.containsKey(temp)) {
                windows.put(temp, windows.getOrDefault(temp,0) + 1);
                // 注意：Integer不能用==比较，要用compareTo
                //只有次数相等的时候，匹配数会＋1
                if (windows.get(temp).compareTo(needs.get(temp)) == 0 ) {
                    // 字符temp出现次数符合要求，match代表符合要求的字符个数
                    match++;
                }
            }
            //右移窗口
            // 优化到不满足情况，right继续前进找可行解
            right++;
            //此时窗口[left,right)

            //（五）左移：判断左移，获取移出，更新，左移
            //判断左侧窗口是否要收缩
            // 符合要求的字符个数正好是need中所有字符，获得一个可行解
            while (match == needs.size()) {
                //如果更短则更新结果
                if (right - left < minLen) {
                    start = left;
                    minLen = right - left;
                }
                // d 是将移出窗口的字符
                // 开始进行优化，即缩小区间，删除s[left],
                char c = s.charAt(left);
                // 当前删除的字符包含于need,更新Windows中对应出现的次数
                // 如果更新后的次数<need中出现的次数，符合要求的字符数减1
                // 下次判断match==needs.size()时不满足情况，
                if (needs.containsKey(c)) {
                    windows.put(c, windows.get(c)  - 1);
                    if (windows.get(c) < needs.get(c)){
                        match--;
                    }
                }
                left++;
            }
        }
        // 返回覆盖的最小串
        if (minLen==Integer.MAX_VALUE){
            return "";
        }
        return s.substring(start, minLen + start);

    }
}
```

[permutation-in-string](https://leetcode-cn.com/problems/permutation-in-string/)

> 给定两个字符串  **s1**  和  **s2**，写一个函数来判断  **s2**  是否包含  **s1 **的排列。



```java
class Solution {
/**
- 1、当移动 right 扩大窗口，即加入字符时，应该更新哪些数据？ window,match
- 2、什么条件下，窗口应该暂停扩大，开始移动 left 缩小窗口？ 窗口长度为s1.length
- 3、当移动 left 缩小窗口，即移出字符时，应该更新哪些数据？ window match
- 4、我们要的结果应该在扩大窗口时还是缩小窗口时进行更新？ 缩小窗口时
**/

    public static boolean checkInclusion(String s1, String s2) {
        HashMap<Character,Integer> need=new HashMap<>();
        HashMap<Character,Integer> window=new HashMap<>();
        //（一）初始化need目标，window窗口
        for(int i=0;i<s1.length();i++){
            need.put(s1.charAt(i),need.getOrDefault(s1.charAt(i),0)+1);
        }
        //（二）初始化窗口的左右指针
        int left=0,right=0,match=0;


        //（三）右指针在控制窗口
        while(right<s2.length()){

            //（四）右移：获取移入，更新，右移
            char c=s2.charAt(right);
            if(need.containsKey(c)){
                window.put(c,window.getOrDefault(c,0)+1);
                if(window.get(c).equals(need.get(c))){
                    match++;
                }
            }
            right++;

            //（五）左移：判断左移，获取移出，更新，左移
            while(right-left==s1.length()){
                if(match==need.size()){
                    return true;
                }

                char temp=s2.charAt(left);

                if(need.containsKey(temp)){
                 if (window.get(temp).equals(need.get(temp))){
                      match--;
                  }
                  window.put(temp, window.get(temp) - 1);
                  
                }
                
                left++;
            }

        }
        return false;
    }
}
```

[find-all-anagrams-in-a-string](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

> 给定一个字符串  **s **和一个非空字符串  **p**，找到  **s **中所有是  **p **的字母异位词的子串，返回这些子串的起始索引。

```java
class Solution {
public List<Integer> findAnagrams(String s, String p) {
        HashMap<Character,Integer> need=new HashMap<>();
        HashMap<Character,Integer> window=new HashMap<>();
        //（一）初始化need目标，window窗口
        for(int i=0;i<p.length();i++){
            need.put(p.charAt(i),need.getOrDefault(p.charAt(i),0)+1);
        }
        //（二）初始化窗口的左右指针
        int left=0,right=0,match=0;
        //结果
        LinkedList<Integer> result=new LinkedList<>();

        //（三）右指针在控制窗口
        while(right<s.length()){

            //（四）右移：获取移入，更新，右移
            char c=s.charAt(right);
            if(need.containsKey(c)){
                window.put(c,window.getOrDefault(c,0)+1);
                if(window.get(c).equals(need.get(c))){
                    match++;
                }
            }
            right++;

            //（五）左移：判断左移，获取移出，更新，左移
            while(right-left==p.length()){
                if(match==need.size()){
                    result.add(left);
                }

                char temp=s.charAt(left);

                if(need.containsKey(temp)){
                 if (window.get(temp).equals(need.get(temp))){
                      match--;
                  }
                  window.put(temp, window.get(temp) - 1);
                  
                }
                
                left++;
            }

        }
        return result;
    }
}
```

[longest-substring-without-repeating-characters](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

> 给定一个字符串，请你找出其中不含有重复字符的   最长子串   的长度。
> 示例  1:
>
> 输入: "abcabcbb"
> 输出: 3
> 解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

```java
lass Solution {
    public int lengthOfLongestSubstring(String s) {
        
        //（一）初始化window窗口
        HashSet<Character> window=new HashSet<Character>();
        //（二）初始化窗口的左右指针
        int left=0,right=0;
        //结果
        int maxLength=0;

        //（三）右指针在控制窗口
        while(right<s.length()){

            //（四）右移：获取移入，更新，右移
           char rightchar=s.charAt(right);
           if(!window.contains(rightchar)){
               window.add(rightchar);
               right++;
           }else{
                //（五）左移，做指针移动 更新长度
                while(window.contains(rightchar)){
                     if(right-left>maxLength){
                        maxLength=right-left;
                     }
                     window.remove(s.charAt(left));
                     left++;
                }
              
           }
            
        }
        //有可能没有左移过，最后更新长度
         if(right-left>maxLength){
             maxLength=right-left;
           }
      
        return maxLength;
    }
    
}

```

## 总结

- 和双指针题目类似，更像双指针的升级版，滑动窗口核心点是维护一个窗口集，根据窗口集来进行处理
- 核心步骤
  - right 右移
  - 收缩
  - left 右移
  - 求结果


