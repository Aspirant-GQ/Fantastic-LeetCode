
# 题目

>给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0)。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。说明：你不能倾斜容器，且 n 的值至少为 2。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200902092242504.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)




# 思路一：暴力循环

两层嵌套for循环，逐个排除奥力给！！！

```c
int maxArea(int* height, int heightSize){
    int i, j, area=0, max_area=0;
    int now_height=0, width=0;
    for( i=0; i<heightSize; i++ ){
        for( j=i+1; j<heightSize; j++ ){
            if( height[i]==0 ){
                break;
            }
            now_height = height[i]<height[j]?height[i]:height[j];//高度
            width = j-i;//宽度
            area = now_height*width;//更新当前面积
            max_area = area>max_area?area:max_area;//更新最大面积
        }
    }
    return max_area;
}
```



结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200902092256140.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)


显然这种方法虽然可行，但是时间复杂度显然不行，所以要寻求更优解。



# 思路二：双指针

采用双指针的方法来解题，思路是：

刚开始双指针指向数组的两端，开始运算得到当前的面积，然后找出比较低的那一端，将低的那一端向中间移动一位，进行下一次计算面积，每次计算完面积之后与当前最大面积比较（采用三目运算符也可以）。

注意：每次只移动一个指针，肯定是移动低的那一个，因为==容器盛水的高度取决于低的那一边，如果移动高的那一边，那么高度不变（还是低的那一端）宽度反而变小了，这样只会越来越小==。

初代代码如下：

```c
int maxArea(int* height, int heightSize){
    int i ,j , area=0, max_area=0, min_number=0;
    int now_height=0, width=0;
    /* 双指针：i对应左端  j对应右端 */
    for( i=0, j=heightSize-1; i<j;  ){
        now_height = height[i]<height[j]?height[i]:height[j];//更新高度
        width = j-i;//更新宽度
        area = now_height*width;//更新面积
        if( height[i]<=height[j] ){
            i++;
        }
        else{
            j--;
        }
        max_area = area>max_area?area:max_area;//更新最大面积
    }
    return max_area;
}
```

运行结果如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200902092308479.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQzNzQzNzYy,size_16,color_FFFFFF,t_70#pic_center)


可以看出，虽然执行时间有了大幅度提升，但貌似内存消耗更大了。

可再次优化点：

* 宽度计算优化：之前的每次采用`width = j-i;`来更新宽度，现在可以每次width--来更新宽度，而且可以放在for循环的头中：` for( i=0, j=heightSize-1; i<j; width-- )`；
* 面积为0判断：如果传入的指针为空，或者数组大小小于2，则面积为0，直接返回



优化后：

```c
int maxArea(int* height, int heightSize){
    int i ,j , area=0, max_area=0, min_number=0;
    int now_height=0, width=heightSize-1;
    
    /* 判断面积为0 */
    if( heightSize<2 || height==NULL ){
        return 0;
    }
    
    /* 双指针：i对应左端  j对应右端 */
    for( i=0, j=heightSize-1; i<j; width-- ){//更新宽度
        now_height = height[i]<height[j]?height[i]:height[j];//更新高度
        area = now_height*width;//更新面积
        if( height[i]<height[j] ){
            i++;//移动指针
        }
        else{
            j--;
        }
        max_area = area>max_area?area:max_area;//更新最大面积
    }
    return max_area;
}
```

