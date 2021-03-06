# LeetCode_3_无重复字符的最长子串

## 题目

>给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度
![hh](https://img-blog.csdnimg.cn/20200726164806593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70)

## 思路一：暴力法

### 第一次

刚开始的想法是：逐个检查字符串中的字符，并且在hash数组中建立映射，与字符的ASCII码一一对应，通过检查hash数组中的标志来判断相同的字符是否出现过，如果已经出现，则记录重复的字符并再次初始化hash，以重复的字符为起点重新检查。

代码：

```c
int lengthOfLongestSubstring(char * s)
{
    int count=0,count_max=0,i,x,hash[150],repetition_count;

    memset( hash, 0, 150 );//初始化hash,全部为0

    i=0;
    x=strlen(s);

    //while( *(s+i)!= NULL )
    for( i=0; i<x; i++ )
    {
       
        /* 重复 */
        if( hash[ *(s+i) ]==1 )
        {
            if( count>count_max ) count_max = count;  //记录新的最大长度
            count = 1; //开始新一轮长度计数
            memset( hash, 0, 150 );
            hash[ *(s+i) ]=1;
        }

        /* 无重复：计数+下一个 */
        else
        {
            hash[ *(s+i) ] = 1; //对应ASCII码
            count++;
        }
        
       // i++;
    }

    if( count>count_max ) count_max = count;
    return count_max;
}
```

但是会遇到提交错误如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200726180448276.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70)
原因是：我判断重复之后，是直接以当前重复的字符为起点来进行计数的，实际中遇到`"dvdf"`这种情况，就会误判为最长为`"df"`，没有从第一个重复的字符处重新判断！！！

### 第二次

然后，我新增了一个变量，用来记录最近的重复字符的位置信息，代码如下：

```c
int lengthOfLongestSubstring(char * s)
{
    int count=0,count_max=0,last=1,i,first=0,repetition_count;  //last记录上一个重复字符的位置，也就是最新的重复字符的位置
    int hash[150]={0,0,0};
    //memset( hash, 0, 150 );//初始化hash,全部为0

    i=0;
    
    for( i=0; i<strlen(s); i++ )
    {
        
        /* 重复 */
        if( hash[ *(s+i) ]>0 )
        {
            if( first==0 )//第一次记录重复
            {
                last = hash[ *(s+i) ];
                first = 1;
            }

            if( count>count_max ) count_max = count;  //记录新的最大长度
            count = i+1 - last;
            hash[ *(s+i) ]=(1+i) ;       //更新位置
            last = i+1;                 //更新最近的重复字符的位置
        }

        /* 不重复 */
        else
        {
            hash[ *(s+i) ] = i+1; //对应ASCII码
            count++;
        }
    }

    if( count>count_max ) count_max = count;
    return count_max;
}
```

然鹅，又出错了。。。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200726224753220.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70)

### 第三次

分析原因是，在连续两次遇到重复字符的之后，如果两次的重复字符不一样，可以把当前遇到的重复字符算进去，这样新代码就如下：

```c
int lengthOfLongestSubstring(char * s)
{
    int count=0,count_max=0,last=1,i,first=0,repetition_count;  //last记录上一个重复字符的位置，也就是最新的重复字符的位置
    int hash[150]={0,0,0};
    //memset( hash, 0, 150 );//初始化hash,全部为0

    i=0;
    
    for( i=0; i<strlen(s); i++ )
    {
        
        /* 重复 */
        if( hash[ *(s+i) ]>0 )
        {
            if( first==0 )//第一次记录重复
            {
                last = hash[ *(s+i) ];
                first = 1;
            }

            if( count>count_max ) count_max = count;  //记录新的最大长度

                count = i+1 - last;
                if( *(s+i)!=*(s+last-1) ) count++;
                hash[ *(s+i) ]=(1+i) ;       //更新位置
                last = i+1;                 //更新最近的重复字符的位置
        }

        /* 不重复 */
        else
        {
            hash[ *(s+i) ] = i+1; //对应ASCII码
            count++;
        }
    }

    if( count>count_max ) count_max = count;
    return count_max;
}
```

实际上就是加了一行代码：`if( *(s+i)!=*(s+last-1) ) count++;`，用来判断当前重复字符是否是前一个重复的字符，如果不同则可以计数。
结果显而易见，又错了。。。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200727104307289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70)
幸运的是，每一次改代码，都是在不断通过新的测试用例。

### 第四次



270

```c
int lengthOfLongestSubstring(char * s)
{
    int count=0,count_max=0,last=1,i,first=0,repetition_count;  //last记录上一个重复字符的位置，也就是最新的重复字符的位置
    int hash[150]={0,0,0};
    //memset( hash, 0, 150 );//初始化hash,全部为0

    i=0;
    
    for( i=0; i<strlen(s); i++ )
    {
        
        /* 重复 */
        if( hash[ *(s+i) ]>0 )
        {
            if( first==0 )//第一次记录重复
            {
                last = hash[ *(s+i) ];
                first = 1;
            }

            if( count>count_max ) count_max = count;  //记录新的最大长度

            count = i+1 - last;
            if( *(s+i)!=*(s+last-1) && ( hash[ *(s+i) ] < last ) ) count++;//当前重复字符是否和前一个重复字符相同，如果不同则可以计数！前提是当前重复字符在上一个重复字符之前
            hash[ *(s+i) ]=(1+i) ;       //更新位置
            last = i+1;                 //更新最近的重复字符的位置
        }

        /* 不重复 */
        else
        {
            hash[ *(s+i) ] = i+1; //对应ASCII码
            count++;
        }
    }

    if( count>count_max ) count_max = count;
    return count_max;
}
```





放弃了，发现我的想法和滑动窗口的思想差不多，尝试用滑动窗口算法结合Hash解题。

## 思路二:嵌套for，逐个排除

这个算法太莽了，不到万不得已不采用！

## 思路三：滑动窗口+Hash

窗口滑动算法经常原来解决一些查找满足一定条件的连续区间的性质（长度）问题，类似于==“请找到满足xx条件的最xx的区间（可以是子串、子数组）的xx”==。所以，==窗口滑动算法是在一个特定大小的字符串上或者是一个数组上进行的操作，而不是对整个字符串或者数组进行操作==，这样就极大的降低了时间复杂度，降低了循环嵌套的深度。

代码如下：

```c
int lengthOfLongestSubstring(char * s)
{
    int i=0,start=0;
    int count=0,max_length=0;
    int Hash[256]={0,0};//Hash中存放字符的位置，Hash的下标对应字符的ASCII码  
    if( s[0]=='\0' ){
        return 0;
    }
    for( i=0;s[i]!='\0';i++ ){
        //如果上次的位置大于start的位置，代表重复
        if( Hash[s[i]]>start ){
            count = i-start;//更新长度
            max_length = count>max_length?count:max_length;//更新最大长度
            start = Hash[s[i]];//直接移动到上次重复位置开始 
        }
        Hash[s[i]] = i+1;//更新位置
    }
    count = i-start;
    max_length = count>max_length?count:max_length;
    return max_length;
}
```

执行结果：
![1598863307625](https://img-blog.csdnimg.cn/20200831164619263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)



（PS：为什么相同的代码，提交多次会有不一样的结果？）

变量的意义：

* Hash的下标对应字符的ASCII值，Hash中存放的是对应字符的位置

* start记录子串的起始位置

* count记录出现重复的时候子串长度，通过start与当前字符位置的差来求，即count = i-start;所以子串的长度只和当前字符位置与当前子串起始地址有关

* max_length记录最长子串的长度，在每一次count更新后，更新max_length



思路：
==首先根据滑动窗口的思想，要遍历字符串，从最左端开始，逐个向右进行排查，每遇到一个字符就在Hash中查询当前字符的上一个位置，如果当前字符的上一个位置在当前子串起始位置之前，就代表当前子串中没有重复的字符（当前字符不重复），然后更新当前字符在Hash中的位置。如果当前字符的上一个位置在当前子串起始位置之后，就代表当前字符已经在子串中存在，意味着字符重复，终结不重复子串，所以要更新count，更新max_length，并且最重要的：将子串的起始位置移动到重复字符的上一个位置上，开始下一轮子串计数。最后在遍历完字符串之后，再进行一次子串长度更新！==

