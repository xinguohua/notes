# 链表

## 基本技能

链表相关的核心点

- dummy node 哑巴节点
- 快慢指针
- 插入一个节点到排序链表
- 从一个链表中移除一个节点
- 翻转链表
- 合并两个链表
- 找到链表的中间节点

## 常见题型
### 一 dummy node 哑巴节点
#### [partition-list](https://leetcode-cn.com/problems/partition-list/)

> 给定一个链表和一个特定值 x，对链表进行分隔，使得所有小于  *x*  的节点都在大于或等于  *x*  的节点之前。

思路：构造两个链表，beforeHead小于x的节点，afterHead大于x的节点，最后将他们连在一起

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        // 构造两个链表，beforeHead小于x的节点，afterHead大于x的节点，最后将他们连在一起
        // 这里的技巧就是给两个链表都搞一个哑巴节点，也就是指向头结点的节点
        ListNode beforeHead = new ListNode(-1);
        ListNode before = beforeHead;
        ListNode afterHead = new ListNode(-1);
        ListNode after = afterHead;
        while (head != null) {
            if (head.val < x) {
                before.next = head;
                before = before.next;
            } else {
                after.next = head;
                after = after.next;
            }
            head = head.next;
        }
		before.next = afterHead.next;
        after.next = null;
        return beforeHead.next;
    }
}
```

哑巴节点使用场景

> 当头节点不确定的时候，使用哑巴节点
### 二 快慢指针
### 二-1 快2慢1指针
### [linked-list-cycle](https://leetcode-cn.com/problems/linked-list-cycle/)

> 给定一个链表，判断链表中是否有环。

思路：快慢指针，快慢指针相同则有环，如果有环每走一步快慢指针距离会减 1，最终重合（也就是套圈）。

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if (head == null) {
            return false;
        }
        ListNode slow = head, fast = head;
        boolean flag = false;
        while (slow.next != null && fast.next != null) {
            slow = slow.next;
            if (fast.next.next != null) {
                fast = fast.next.next;
            } else {
                break;
            }
            if (fast == slow) {
                flag = true;
                break;
            }
        }
        return flag;
    }
}
```

### [linked-list-cycle-ii](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

> 给定一个链表，返回链表开始入环的第一个节点。  如果链表无环，则返回  `null`。

思路：快慢指针，快慢相遇之后，慢指针回到头，快慢指针步调一致一起移动，相遇点即为入环点

[详解见](https://leetcode-cn.com/problems/linked-list-cycle-ii/solution/xiang-xi-tu-jie-ken-ding-kan-de-ming-bai-by-xixili/)


```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if(head==null || head.next==null)
            return null;
        ListNode slow=head,fast=head;
        //有无环路
        boolean flag=false;
        //判断有无环路，并找到第一次相遇的地方
        while(slow.next!=null&&fast.next!=null){
            slow=slow.next;
            if(fast.next.next!=null){
                fast=fast.next.next;
            }else{
                break;
            }
            if(fast==slow){
                flag=true; //第一次相遇
                System.out.println(fast.val);
                break;
            }
        }
        slow=head;
        //第二次相遇:slow到头结点，fast到相遇节点步调一致运动相遇
         while(flag&&slow.next!=null&&fast.next!=null){
             if(fast==slow){
                return slow;
            }
             slow=slow.next;
             fast=fast.next; 
         }
        
        return null;    
    }
}
```


### 二-2 相邻快慢指针
#### [remove-duplicates-from-sorted-list](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

> 给定一个排序链表，删除所有重复的元素，使得每个元素只出现一次。


```java
class Solution {
public ListNode deleteDuplicates(ListNode head) {
        //伪节点
        ListNode dumpy=new ListNode(Integer.MAX_VALUE);
        dumpy.next=head;
        //慢指针
        ListNode pre=dumpy;
        //快指针
        ListNode cur=head;
        while(cur!=null){
           //去除重复元素
           while(cur!=null&&pre.val==cur.val){
               cur=cur.next;
           }
           //快指针指向第一个不同的元素
           //第一个不同的元素不为空
           if(cur!=null){
                pre.next=cur;
                pre=cur;
                cur=cur.next;
           }else{
               //为空
                pre.next=null;
           }
        }
        return dumpy.next;
    }
}
```
#### [remove-duplicates-from-sorted-list-ii](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list-ii/)

> 给定一个排序链表，删除所有含有重复数字的节点，只保留原始链表中   没有重复出现的数字。

思路：链表头结点可能被删除，所以用 dummy node 辅助删除
注意点
* A->B->C 删除 B，A.next = C
* 删除用一个 Dummy Node 节点辅助（允许头节点可变,相等的时候pre移动）
* 访问 cur.next一定要保证 cur != null

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
		//伪节点
        ListNode dummy = new ListNode(Integer.MAX_VALUE);
        dummy.next = head;
		//慢指针 待判断的前一个
        ListNode pre = dummy;
		//快指针 要判断的
        ListNode cur = head.next;
		//去除重复元素
        while (cur != null) {
            if (pre.next.val != cur.val) {
                pre = pre.next;
                cur = cur.next;
            } else {
                while (cur != null && pre.next.val == cur.val) {
                    cur = cur.next;
                }
                pre.next = cur;
				if(cur !=null){
				    cur=cur.next;
				}
            }
        }
        return dummy.next;
    }
}
```
### 三 插入一个节点到排序链表
#### [147.对链表进行插入排序](https://leetcode-cn.com/problems/insertion-sort-list/)
```java 
class Solution {
  public ListNode insertionSortList(ListNode head) {
        //特例  至少有两个元素为有序
        if (head==null || head.next==null)
            return head;
        //伪节点    
        ListNode dummy = new ListNode(-1);
        dummy.next=head;
        //0 定义指针
        //指向有序序列最后一个元素的指针（初始为第一个元素）
        ListNode order=dummy.next;
        //指向待排元素的指针(初始为第二个元素)
        ListNode ready=order.next;


        //1 每次迭代中，插入排序只从输入数据中移除一个待排序的元素
        while (ready!=null){
            // 直接插入末尾
            if (ready.val>order.val){
                order=order.next;
                ready=ready.next;
            }else {
                //断链
                order.next = null;

                //双指针寻找插入的地方
                ListNode pre = dummy;
                ListNode cur = dummy.next;
                ListNode temp=ready.next;
                while (cur != null) {
                    
                    if (cur.val>=ready.val) {
                        //在cur前插入
                        pre.next = ready;
                        ready.next = cur;
                        break;
                    }
                    pre = cur;
                    cur = cur.next; 
                }

                //连链
                ready=temp;
                order.next=ready;        
            }
        }

        return dummy.next;

    }
}
```

### 四 从一个链表中移除一个节点
#### [237. 删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)
```java
class Solution {
    public void deleteNode(ListNode node) {
        node.val=node.next.val;
        node.next=node.next.next;
    }
}
```
#### [203. 移除链表元素](https://leetcode-cn.com/problems/remove-linked-list-elements/)
```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
            //伪节点
            ListNode dummpy=new ListNode(-1);
            dummpy.next=head;
            
            //双指针
            ListNode pre=dummpy;
            ListNode cur=head;
            while(cur!=null){
                if(cur.val==val){
                    pre.next=cur.next;
                }else{
                    //删了pre不走 没删前进
                    pre=pre.next;
                }
                cur=cur.next;
            }
            return dummpy.next;
    }
}
```

### 五 翻转链表

#### [reverse-linked-list](https://leetcode-cn.com/problems/reverse-linked-list/)

> 反转一个单链表。

**思路**：用一个 newHead 做头结点，将链表依次插到头结点后面。头插法

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode dumpy = new ListNode(-1);
        while (head != null) {
			//下一个要插入的
            ListNode next = head.next;
			//向newhead插入head
            head.next = dumpy.next;
            dumpy.next = head;
			//head移到下一个要插入的地方
            head = next;
        }
        return dumpy.next;
    }
}
```
**思路**：递归解法

```java
class Solution {
    //输入一个节点 head，将「以 head 为起点」的链表反转，并返回反转之后的头结点。
     public ListNode reverseList(ListNode head) {
        //特例终止条件
		//链表只有一个节点的时候反转也是它自己
         if (head==null|| head.next == null)
            return head;
		//新的头结点是 last	
        ListNode LastNode = reverseList(head.next);
		//反转过后的链表与head相连
        head.next.next = head;
		//而之前的 head 变成了最后一个节点
		head.next = null;
        return LastNode;
    }
}
```
#### [reverse-linked-list-ii](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

> 反转从位置  *m*  到  *n*  的链表。请使用一趟扫描完成反转。

思路：和反转整个链表差不多，只要稍加修改即可
```java
ListNode successor = null; // 后驱节点

// 将链表的前 n 个节点反转（n <= 链表长度）
ListNode reverseN(ListNode head, int n) {
    if (n == 1) { 
        // 记录第 n + 1 个节点，终止头结点的下一个
        successor = head.next;
        return head;
    }
    // 以 head.next 为起点，需要反转前 n - 1 个节点
    ListNode last = reverseN(head.next, n - 1);
	//反转过后的链表与head相连
    head.next.next = head;
    // 让反转之后的 head 节点和后面的节点（未反转部分）连起来* 
    head.next = successor;
    return last;
}    
```


```java
class Solution {

	ListNode successor = null; // 后驱节点

    public ListNode reverseBetween(ListNode head, int m, int n) {
        ListNode dummy = new ListNode(-1);  // 哑巴节点，指向链表的头部
        dummy.next = head;
        ListNode pre= dummy;  // pre 指向要翻转子链表的第一个节点
        ListNode cur=head;
        
        //遍历找到开始反转的地方
        int index=1;
        while (cur!=null){
            if (index==m){
                //开始反转的地方
                break;
            }
            pre=cur;
            cur=cur.next;
            index++;
        }    
       	
		ListNode newhead=reverseN(cur,n-m+1);
        pre.next=newhead;
        return dummy.next;
    }


		
		// 反转以 head 为起点的 n 个节点，返回新的头结点
		ListNode reverseN(ListNode head, int n) {
		    if (n == 1) { 
		        // 记录第 n + 1 个节点
		        successor = head.next;
		        return head;
		    }
		    // 以 head.next 为起点，需要反转前 n - 1 个节点
		    ListNode last = reverseN(head.next, n - 1);
		
		    head.next.next = head;
		    // 让反转之后的 head 节点和后面的节点连起来
		    head.next = successor;
		    return last;
		}  

}
```
### 六 合并两个链表
#### [merge-two-sorted-lists](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

> 将两个升序链表合并为一个新的升序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。

思路：迭代

```java
class Solution {
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
       ListNode cur1=l1;
       ListNode cur2=l2;
       //伪头节点
       ListNode dumpy=new ListNode(0);
       ListNode cur=dumpy;
       //公共部分
       while (cur1!=null&&cur2!=null){
           if (cur1.val<=cur2.val){
               cur.next= cur1;
               cur1=cur1.next;

           }else{
               cur.next = cur2;
               cur2=cur2.next;
           }
           cur=cur.next;
       }
       //剩余部分
       if (cur1!=null){
            cur.next=cur1;
       }else{
           cur.next=cur2;
       }
       return dumpy.next;
}
```



### 七 找到链表的中间节点
#### [876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)
- 在两个中间结点的时候，返回第二个中间结点
```java 
    public ListNode middleNode(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }
        ListNode slow=head;
        ListNode fast=head;
        while(fast!=null&&fast.next!=null){
            slow=slow.next;
            fast=fast.next.next;
        }
        return slow;
    }
```
- 在两个中间结点的时候，返回第一个中间结点
```java 
    public ListNode middleNode(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }
        ListNode slow=head;
        ListNode fast=head;
        while(fast.next!=null&&fast.next.next!=null){
            slow=slow.next;
            fast=fast.next.next;
        }
        return slow;
    }
```

> 在  *O*(*n* log *n*) 时间复杂度和常数级空间复杂度下，对链表进行排序。
##### 递归版链表的归并排序（自顶向下）

思路：归并排序，找中点和合并操作

```java
class Solution {
     public ListNode sortList(ListNode head) {
        // 递归结束条件(分组后数组只有一个元素时不再分组)
        if (head == null || head.next == null) {
            return head;
        }

		//找到链表中点，返回第一个中间节点
        ListNode slow = head, fast = head;
        while (fast.next != null && fast.next.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        ListNode rightHead = slow.next;
        slow.next = null;
		
		//分治：递归分成左右两组
        ListNode left = sortList(head);
        ListNode right = sortList(rightHead);
		//合并
        return mergeLists(left, right);
    	}

    private ListNode mergeLists(ListNode left, ListNode right) {
		//伪节点
        ListNode dummy = new ListNode(-1);
        ListNode curNode = dummy;
		//开始合并
        while (left != null && right != null) {
            if (left.val < right.val) {
                curNode.next = left;
                left = left.next;
            } else {
                curNode.next = right;
                right = right.next;
            }
            curNode = curNode.next;
        }
		//剩余部分
       if (left!=null){
            curNode.next=left;
       }else{
           curNode.next=right;
       }
        return dummy.next;
    }
}
```
注意点

- 快慢指针 判断 fast.next 及 fast.next.next 是否为 null 值，返回第一个中间节点
- 递归 mergeSort 需要断开中间节点
- 递归返回条件为 head 为 null 或者 head.Next 为 null

##### 迭代版链表的归并排序（自底向上）
下面是另一种解法类似归并排序，相当于用迭代模拟归并的过程。
时间复杂度O(nlogn),空间复杂度O(1)

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
    public ListNode sortList(ListNode head) {
        //1 得首先知道链表多长
        ListNode dummy = new ListNode(-1);
        dummy.next=head;
        int length=getListLength(dummy);
        //2每轮分组
        for (int sublength = 1; sublength <length ; sublength=sublength*2) {
            //指向剩余部分的指针
			//pre为排列有序的指针，h1割出来的第一部分，h2割出来第二部分，cur 当前指针
            ListNode pre=dummy;
            ListNode cur=dummy.next;
            ListNode h1 = dummy.next;;
            //3相邻分组merge
            //3.1 一块从原链表分不断割出相邻量组加剩余部分 sublength+sublength+剩余
            while (cur!=null) {
                ListNode h2 = split(h1, sublength);
                cur = split(h2, sublength);

                //3.2 相邻两组merge,前与有序链表连接
                ListNode orderhead=merge(h1, h2);

                //3.3 与前面已经排好序的链表连接
                pre.next=orderhead;
                //3.4 pre到排好序的尾部
                while (pre.next!=null){
                    pre=pre.next;
                }
                h1=cur;
            }
        }

        return dummy.next;
    }

    private ListNode merge(ListNode h1, ListNode h2) {
        ListNode dummy = new ListNode(-1);
        ListNode p = dummy;
        while (h1!=null&&h2!=null){
            if (h1.val<h2.val){
                p.next=h1;
                h1=h1.next;
            }else{
                p.next=h2;
                h2=h2.next;
            }
            p=p.next;
        }
        if(h1!=null){
            p.next=h1;
        }else if(h2!=null){
            p.next=h2;
        }

        return dummy.next;
    }

	//返回割完链表 下一链表的头结点
    private ListNode split(ListNode head, int sublength) {
        int cnt=0;
        ListNode dummy = new ListNode(-1);
        dummy.next=head;
        while (head !=null&&cnt<sublength){
            head=head.next;
            dummy=dummy.next;
            cnt++;
        }
        dummy.next=null;
        return head;
    }

    private int getListLength(ListNode head) {
        int count=0;
        while (head.next!=null){
            head=head.next;
            count++;
        }
        return count;
    }
}
```


### [reorder-list](https://leetcode-cn.com/problems/reorder-list/)

![](http://wardseptember.club/FjzGBhybSvpYFsxvaJGI5PlJpBrs)


思路：找到中点断开，翻转后面部分，然后合并前后两个链表

```java
class Solution {
     public void reorderList(ListNode head) {
        if(head==null||head.next==null)
            return;
        //1 返回第一个中间节点
        ListNode middle = middleNode(head);
        //2 切断第二个链表
        ListNode second=middle.next;
        middle.next=null;
        //3 反转第二个链表
        ListNode secondhead=reverseList(second);
        
        //4 合并两个链表
        ListNode results =  mergeTwoLists(head,secondhead);
        
        return;

    }

    public ListNode middleNode(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }
        ListNode slow=head;
        ListNode fast=head;
        while(fast.next!=null&&fast.next.next!=null){
            slow=slow.next;
            fast=fast.next.next;
        }
        return slow;
    }


     //输入一个节点 head，将「以 head 为起点」的链表反转，并返回反转之后的头结点。
     public ListNode reverseList(ListNode head) {
        //特例终止条件
		//链表只有一个节点的时候反转也是它自己
         if (head==null|| head.next == null)
            return head;
		//新的头结点是 last	
        ListNode LastNode = reverseList(head.next);
		//反转过后的链表与head相连
        head.next.next = head;
		//而之前的 head 变成了最后一个节点
		head.next = null;
        return LastNode;
    }


    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
       ListNode cur1=l1;
       ListNode cur2=l2;
       //伪头节点
       ListNode dumpy=new ListNode(0);
       ListNode cur=dumpy;
       boolean flag=true;
       //公共部分
       while (cur1!=null&&cur2!=null){
           if (flag){
               cur.next= cur1;
               cur1=cur1.next;

           }else{
               cur.next = cur2;
               cur2=cur2.next;
           }
           cur=cur.next;
           flag=!flag;
       }
       //剩余部分
       if (cur1!=null){
            cur.next=cur1;
       }else{
           cur.next=cur2;
       }
       return dumpy.next;
}
}
```





### [palindrome-linked-list](https://leetcode-cn.com/problems/palindrome-linked-list/)

> 请判断一个链表是否为回文链表。

切成两半，把后半段反转，然后比较两半是否相等。
```java
    public boolean isPalindrome(ListNode head) {
        //特例
        if(head==null||head.next==null){
            return true;
        }
        //1 返回第一个中间节点
        ListNode middle=middleNode(head);
        //2 切成两半
        ListNode secondhead=middle.next;
        middle.next=null;
        //3 反转第二个链表
        ListNode h2=reverseList(secondhead);
        //4 比较两个链表
        return compareTwoLists(head,h2);
    }


    //返回第一个中间节点
      public ListNode middleNode(ListNode head) {
        if(head==null||head.next==null){
            return head;
        }
        ListNode slow=head;
        ListNode fast=head;
        while(fast.next!=null&&fast.next.next!=null){
            slow=slow.next;
            fast=fast.next.next;
        }
        return slow;
      }

     
     //输入一个节点 head，将「以 head 为起点」的链表反转，并返回反转之后的头结点。  
     public ListNode reverseList(ListNode head) {
        //特例终止条件
		//链表只有一个节点的时候反转也是它自己
         if (head==null|| head.next == null)
            return head;
		//新的头结点是 last	
        ListNode LastNode = reverseList(head.next);
		//反转过后的链表与head相连
        head.next.next = head;
		//而之前的 head 变成了最后一个节点
		head.next = null;
        return LastNode;
    }

    //比较两个链表
    public boolean compareTwoLists(ListNode l1, ListNode l2) {
       ListNode cur1=l1;
       ListNode cur2=l2;
      
       //公共部分
       while (cur1!=null&&cur2!=null){
           if (cur1.val !=cur2.val){
               return false;
           }else{
               cur1=cur1.next;
               cur2=cur2.next;
           }
       }
      return true;
}
```







