

# 题目

>给定一个整数类型的数组 nums，请编写一个能够返回数组 “中心索引” 的方法。
>
>我们是这样定义数组 中心索引 的：数组中心索引的左侧所有元素相加的和等于右侧所有元素相加的和。
>
>如果数组不存在中心索引，那么我们应该返回 -1。如果数组有多个中心索引，那么我们应该返回最靠近左边的那一个。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200909195402307.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)


注意：这道题中对数组两端也算在索引的范围内

# 思路一：双指针，两边向中间靠拢

本来的思路是，利用双指针，从数组两端开始向中间靠拢，这样我以为当两个指针相遇的时候就是产生中心索引的时候，但是欠缺考虑，没有充分考虑到负数的情况，导致在后面的测试例子中不通过。所以这种思路我觉得是费力不讨好的，果断换一种思路。

```c
int pivotIndex(int* nums, int numsSize){
    int i, j;
    int left_sum, right_sum;

    if( numsSize<1 ){
        return -1;
    }
    left_sum  = nums[0];
    right_sum = nums[numsSize-1];
    for( i=0,j=numsSize-1; i<j; ){
         if( (left_sum > right_sum) ){
             if( nums[i+1] > 0 ){
                 right_sum += nums[--j];
             }else{
                 left_sum  += nums[++i];
             }
             
         }else{
             if( nums[j-1] > 0 ){
                 left_sum += nums[++i];
             }else{
                 right_sum  += nums[--j];
             }
         }
    }

    if( left_sum==right_sum ){
        return i;
    }else{
        return -1;
    }

}
```



# 思路二：先求和，再遍历排除

先累加，求出所有的数组元素之和，然后开始遍历数组，用左边元素的和与目标值比较，目标值就是当前中心索引下左右两边的元素和的一半，这种情况下的索引是满足条件的，而且是靠近左边的索引。

要注意的是：

* 在i自增之前求和，避免将中心索引的元素也算进去
* 按照题目的需求还要考虑中心索引在数组两端的情况，我开始还专门避免了这种情况，反正就是题目有点迷
* 按照我的方法是：`((float)( sum-nums[i])/2 == left_sum )`用数组总和的值减去中心索引的值，再除以二，与左边的和比较。这种情况下要考虑到数组总和的值减去中心索引的值是一个奇数的问题，因为涉及到的变量都声明了int，所以如果出现小数会自动舍去，所以我使用了强制性的类型转换，转换为float进行比较。（这样对变量本身的值没有影响，因为这里强制类型转换是暂时的）。最后发现可以优化的：`(( sum-nums[i]) == left_sum*2 )`,这样既不需要进行除法运算，也避免了类型转换。

```c
int pivotIndex(int* nums, int numsSize){
    int i, j;
    int left_sum=0, right_sum,sum;

    if( numsSize<1 ){
        return -1;
    }

    for( i=0,sum=0; i<numsSize; i++ ){
        sum += nums[i]; 
    }
    for( i=0; i<numsSize;  ){
        if( ((float)( sum-nums[i])/2 == left_sum )  ){//转换为浮点型就避免了/2是奇数，误判的情况
            return i;
        }
        left_sum += nums[i];//完了之后i自增，到时候直接返回中心索引
        i++;
    }
    return -1;
}
```



运行结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200909195417106.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)
