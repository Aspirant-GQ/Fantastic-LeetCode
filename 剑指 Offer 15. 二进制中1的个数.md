
 [剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)

# 题目

>请实现一个函数，输入一个整数，输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922134459939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)



# 思路：`x &= (x-1)`

```cpp
class Solution {
public:
    int fun (uint32_t x) {
        int count = 0;
        while (x) {
            ++count;
            x &= (x-1);
        }
        return count;
    }

    int hammingWeight(uint32_t n) {
        return fun(n);
    }
};
```

分析：

判断对应的二进制数中有多少个‘1’，通过与运算，原数和原数-1做与运算，改变的是最右边的‘1’，以及‘1’右边的情况。每次运算完毕之后，当前‘1’右边的全为1，当前‘1’左边的全相同（相同的数做与运算不影响），每次运算完毕检查当前的值是否为零。

运行结果：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200922134518646.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)
