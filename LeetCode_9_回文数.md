

# 题目

> 判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。



![在这里插入图片描述](https://img-blog.csdnimg.cn/20200906095856559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)





# 思路：直接得出反转后的数

```c
bool isPalindrome(int x){
    int i, j;
    long int k;
    if( x<0 ){//负数肯定不是
        return false;
    }
    j = x;
    k = 0;
    for( ;j; ){
        k = k*10 + j%10;//直接求出反转后的x，先求低位，然后*10进位，转换为高位
        j/=10;
    }
    
    return ( k==x );//返回类型会自动转换
}
```



结果如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200906095906542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)
