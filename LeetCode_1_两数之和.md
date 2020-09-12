@[TOC]
>给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200725174735711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70)

# 思路一：暴力循环
我想的第一种思路就是逐一排查、暴力循环，类似于选择排序的操作，虽然很粗暴，但是也可以算出来的。

代码如下：

```c
int* twoSum(int* nums, int numsSize, int target, int* returnSize)
{

    int i,j;
    static int returnBuf[2] ={0,0};
    *returnSize = 2;
    
    for( i=numsSize-1; i>0; i-- )//要注意数组越界访问！！！，不能直接按照数组大小作为下标,此处for循环的次数为 numsSize-1
    {
        for( j=0; j<i; j++ )
        {
            /* 没考虑到负数
            if( nums[i]>target )
            {
                break;
            }
            */
            if( (nums[i]+nums[j])==target )
            {
                returnBuf[0]=j;
                returnBuf[1]=i;
                return returnBuf;  //成功就返回
            }
        }
    }
    

    return returnBuf;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020072518064929.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70)

注意：
1.要**注意数组越界访问！！！**我在最外层的for循环中，开始就忽略了这点，导致数组越界访问出错！
2.注意返回值需要用关键字static修饰，因为**局部变量会在函数返回时销毁**
3.此题中**明确表示每种输入只会对应一个答案，所以在得出答案后就return**，可以减少执行时间
4.本来想根据 nums[i]>target来减轻运算，结果**没考虑到负数的情况**


# 思路二：数组散列法

最初的想法是：将数组中的元素存储在subscript数组中，下标对应着subscript数组中的值，然后对subscript对半查找，但是只能通过一部分的测试，源码如下（错误示范）：

```c
int* twoSum(int* nums, int numsSize, int target, int* returnSize)
{
    //int* subscript = (int*)malloc( numsSize*sizeof(int) );
   // int* subscript = (int*)malloc( target*sizeof(int) );  //申请空间
    int subscript[target+1];
    int i;
    static int returnBuf[2] ={0,0};

    /* 哈希表 */
    for( i=0; i<numsSize; i++ )
    {
        if( nums[i]<=target )
        {
            subscript[nums[i]] = i+1;  //下标加一，这样subscript数组里元素为0的就代表没有
        }
    }


    /* 折半查找 */
    for( i=0; i<(target/2); i++ )
    {
        if( (0!=subscript[i]) && (0!=subscript[target-i]) )
        {
            returnBuf[0] = subscript[i]-1;
            returnBuf[1] = subscript[target-i]-1;
            break;
        }
    }
    *returnSize = 2;

    return returnBuf;
}
```
但是我的第一次想法有很多缺陷：在给定target的情况下，subscript数组的大小是target的大小，这样不仅忽略了存在负数的情况，还可能导致数组占用空间过大


吸取别人的经验之后得出更好的算法，使用数组进行散列， 即将 nums 中的元素值当下标，nums的下标当值存储在 hash 数组中，代码如下：

```c
#define MAX_SIZE 2048
int *twoSum(int *nums, int numsSize, int target, int *returnSize)
{
    int i, hash[MAX_SIZE], *res = (int *)malloc(sizeof(int) * 2);//申请空间

    memset(hash, -1, sizeof(hash)); //初始化，将所有元素替换为-1，以int数据类型传递

    for (i = 0; i < numsSize; i++)
    {
        
        /* 一边存放 一边验证 */
        if (hash[(target - nums[i] + MAX_SIZE) % MAX_SIZE] != -1)  //负数取余
        {
            res[0] = hash[(target - nums[i] + MAX_SIZE) % MAX_SIZE];
            res[1] = i;
            /* 有合适的答案就返回 */
            *returnSize = 2;
            return res;
        }
        hash[(nums[i] + MAX_SIZE) % MAX_SIZE] = i;  //防止负数下标越界，循环散列，因为没有下标是负的，所以前一半放正数，后一半放负数
    }

    /* 到这一步代表没有合适的答案 */
    free(hash);
    *returnSize = 0;
    return res;
}
```

1.初始化使用` memset(hash, -1, sizeof(hash)); `来完成
2.hash数组中，前一半存放正数，后一半存放负数，**采用取余的方法可以实现**
3.采用**一边存放hash，一边验证target的算法**，可以有效减少运行时间
4.也要考虑到没有得到答案的情况，此时==申请的返回值的空间就必须手动释放==
5.返回值要可以正常返回，要么使用static修饰、要么申请空间（这种情况就涉及到释放空间的问题）
