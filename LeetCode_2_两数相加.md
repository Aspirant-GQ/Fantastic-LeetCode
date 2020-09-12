# LeetCode_2_两数相加

## 题目

>给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。
>如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。
>您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

![在这里插入图片描述](../../../%E8%BD%AF%E4%BB%B6%E7%AE%A1%E5%AE%B6/Typora/%E5%9B%BE%E7%89%87%E5%BA%93/20200724115946601.png)

## 错误示范

第一次正式刷题，写的跟shi一样。。。记录一下相当错误的算法（知耻而后勇）

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */

struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2)
{
    struct ListNode *p1,*p2,*p3,*Sub;
    int a,b,i,carry,sub;

    /* 转移对象 */
    p1 = l1;
    p2 = l2;
    Sub = p3;

    carry = 0;

    do 
    {
        a = p1->val;
        b = a + p2->val;
        p3->val += carry;

        if(b>=10)  
        {
            carry = 1;   //满足进位，标志位置一
            sub = b-10; 
        }
        else
        {
            carry = 0;
            sub = b;
        }

        p3->val += sub;
        
        p1 = p1->next;
        p2 = p2->next;
        p3 = p3->next;
    }
    while( (NULL!=p1->next) || (NULL!=p2->next) ) ;

    return Sub;


}
```

思路：最开始拿到这道题的时候，考虑过一些算法，最终选择按照我们小学解加法题的想法来，先将低位两个数相加，看是否有进位carry，如果有，留在下一位加上carry，再继续做对应位的相加。这样每次得到的值都赋给一个对应的节点，这样产生的新链表就是题目要求的格式了。

## 通过测试


```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */

struct ListNode* addTwoNumbers(struct ListNode* l1, struct ListNode* l2)
{
    struct ListNode *p1,*p2,*p3;
    int carry;
    long int b;
    struct ListNode *Sub = (struct ListNode*)malloc(sizeof(struct ListNode));

    p3 = (struct ListNode*)malloc(sizeof(struct ListNode));
    p3->next = NULL;//申请完一个链表空间之后要立即将当前空链表指向NULL

    /* 转移对象 */
    p1 = l1;
    p2 = l2;
    Sub = p3;

    carry = 0;

    while( (NULL!=p1) || (NULL!=p2) || carry ) //全部都指向空指针的时候才跳出
    {
        if( (NULL!=p1) && (NULL!=p2) )
        b = p1->val + p2->val + carry;
     //   p3->val += carry;
        
        if(b>=10)  
        {
            carry = 1;   //溢出进位，标志位置一
            p3->val = (b-10);  //最大为19，直接减去10即可 
        }
        else
        {
            carry = 0;
            p3->val = b;
        }

        if( (NULL!=p1) && (NULL!=p2) )
        {
            p1 = p1->next;
        	p2 = p2->next;
        }
        
        /* 当最后一次的时候，就不能继续分配链表了 */
        if( (NULL==p1) && (NULL==p2) )//判断最后一个链表
        {
            p3->next = NULL; //必须要将当前链表指向NULL
        }
        else
        {
            p3->next = (struct ListNode*)malloc(sizeof(struct ListNode));
            p3 = p3->next;
            p3->next = NULL; //必须要将当前链表指向NULL
        }
    }
    return Sub;
}
```

结果多次修改之后，我总结出以下一些注意事项：
1.使用链表之前记得使用malloc申请空间
2.必须要将当前指针指向NULL，否则会出现野指针现象
3.在使用指针之前一定要判断一下指针非空
4.要多注意细节，考虑特殊情况，为特殊情况做充足的准备
5.写题之前要多想，多审题，尽量尝试用不同的方法解题，吸取前辈的宝贵经验

