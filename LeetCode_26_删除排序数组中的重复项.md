
# 题目

>给定一个排序数组，你需要在 原地 删除重复出现的元素，使得每个元素只出现一次，返回移除后数组的新长度。
>
>不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200908230712587.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)


# 思路：双指针

分析：

拿到题首先分析一下，这是一个已经排好序数组，需要删除数组中重复的元素，而且是就地删除，通过下面的题干知道，所谓的删除并不是将重复的元素如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200908230739697.png#pic_center)


不需要考虑数组中新长度后面的元素，就是说新的数组并不需要删除重复的元素，只需要把前面重复的元素覆盖掉，然后返回新数组的大小，程序就会根据返回的数组大小去数组的前n个位置取元素。

使用双指针，a指针始终指向当前新数组的最后一个元素，使用b指针遍历数组，一旦发现`nums[b]>nums[a]`就代表着找到下一个元素，此时将nums[b]复制给nums[++a]，如此循环至数组完毕！

程序：

```c
int removeDuplicates(int* nums, int numsSize){
    unsigned int a,b;
    if( numsSize<2 ){//判断，如果小于两个元素，直接返回，没有重复
        return numsSize;
    }
    /* 开始遍历 */
    for( a=0,b=0; b<numsSize; b++ ){//无论发生什么，b指针都更新
        if( nums[b]>nums[a] ){//发现新元素
            a++;
            nums[a] = nums[b];
        }
    }
    a++;//数组下标包含了0，所以返回的时候+1，就是数组元素的个数。
    return a;
}
```



运行结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200908230700463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)
