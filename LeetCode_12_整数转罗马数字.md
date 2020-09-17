

# 题目

>罗马数字包含以下七种字符： I， V， X， L，C，D 和 M。
>
>字符          数值
>I             1
>V             5
>X             10
>L             50
>C             100
>D             500
>M             1000
>例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 
>
>通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：
>
>I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
>X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
>C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
>给定一个整数，将其转为罗马数字。输入确保在 1 到 3999 的范围内。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20200905213502334.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)





# 思路一：彻底分解

刚开始想着将num彻底分解，得出num和各个数的商、余数，然后建立数学关系来得出最终的结果。

```c
#include "string.h"

char * intToRoman(int num){
    const char const roman_numerals[8]={'I','V','X','L','C','D','M'};
    char *output_numerals=NULL;
    int preprocess[15]={0}, leve[]={0,1000,500,100,50,10,5,1}, i, j;//预处理结果存储和级别

    j=1;
    /* 预处理num，得到num关于罗马数字所有的系数 */
    for( i=1; i<7; i++ ){
        preprocess[j] = num/leve[i];
        preprocess[j+1] = num%leve[i];
        j = j+2;
        num = preprocess[j];
    }

    /* 进行数制转换 */
    

}
```

写着写着不行了，分解不开，寻求另一种解法。

# 思路二：贪心算法



程序如下：

```c
char * intToRoman(int num){
    char roman_number[][2]={"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
    int  leve[]={1000,900,500,400,100,90,50,40,10,9,5,4,1};
    char *result;
    int i=0, j=0, k=0;

    i = num/leve[0];
    /* 先将可能超出1000的部分处理 */
    if(i>0){
        for( j=0; j<i; j++ ){
            result = strcat( result, roman_number[0]);
        }
    }
    /* 接下来处理小于1000的部分 */
    num = num%leve[0];
    for( i=1; i<13; i++ ){
        if( num<leve[i] ){
            break;
        }
        num = num-leve[i];
        result = strcat( result, roman_number[i]);
    }

    return result;
}
```



出现报错：Address Sanitizer: SEGV on unknown address 0x02007428cac2 (pc 0x00000040247e bp 0x7ffd0a52b010 sp 0x7ffd0a52b000 T0)

原因是：段错误，段错误是指访问的内存超过了系统所给这个程序的内存空间。这里返回的指针指向内容的是函数的栈，在函数调用完毕之后就会释放栈中的数据，所以返回时段错误。

所以尝试着用``static char *result;``来修饰返回值的指针，但是这样返回的值是NULL，因为虽然指向返回值的指针是存储在函数的静态存储区的，函数结束后也不会销毁静态存储区的局部变量的值，但是这个静态存储区指针指向的地址却随着函数执行完毕销毁了。

采用动态分配，再堆上申请一段空间来专门存放，代码如下：

```c
char *result = (char*)malloc(100*sizeof(char));
memset(result, 0, 100 * sizeof(char));
```

现在代码如下：

```c
char * intToRoman(int num){
    char roman_number[13][2]={"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
    int  i, leve[13]={1000,900,500,400,100,90,50,40,10,9,5,4,1};
    char *result = (char*)malloc(100*sizeof(char));
    memset(result, 0, 100 * sizeof(char));
    for( i=0; i<13; ){
        if( num>=leve[i] ){
            strcat( result, roman_number[i]);
            num -= leve[i];
        }
        else{
            i++;//采用这里的i++，来实现num的同一leve重复计算操作
        }
    }
    return result;
}
```

结果是：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200905213519274.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)


分析原因之后发现，是我的roman_number字符串数组出问题了，没有考虑到系统自己加的‘\0’结束符，所以需要更改为：`char roman_number[13][3]`



修改之后代码为：

```c
char * intToRoman(int num){
    char roman_number[13][3]={"M","CM","D","CD","C","XC","L","XL","X","IX","V","IV","I"};
    int  i, leve[13]={1000,900,500,400,100,90,50,40,10,9,5,4,1};
    char *result = (char*)malloc(100*sizeof(char));
    memset(result, 0, 100 * sizeof(char));
    for( i=0; i<13; ){
        if( num>=leve[i] ){
            strcat( result, roman_number[i]);
            num -= leve[i];
        }
        else{
            i++;
        }
        
    }
    return result;
}
```

通过测试：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200905213533160.png#pic_center)
