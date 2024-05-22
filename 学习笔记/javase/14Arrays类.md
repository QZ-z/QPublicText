[toc]
<font size=3>
# 冒泡排序算法
```java
//从大到小
for(int i=arry.length-1;i>0;i--) {
    for (int j = 0; j < i; j++) {
        if (arry[j] < arry[j + 1]) {
            int temp = arry[j];
            arry[j] = arry[j + 1];
            arry[j + 1] = temp;
        }
    }
}
```
# 选择排序算法
``` java
//选择排序，从小到大
for (int i = 0;i<arry.length-1;i++){
    int min = i;
    for(int j=i+1;j<arry.length;j++){
        if(arry[j]<min){
            min=j;
        }
    }
    if(min!=i){
        int temp = arry[i];
        arry[i] = arry[min];
        arry[min] = temp;
    }
}

```
> 冒泡和选择比较次数一样，选择排序交换位置次数少

# 二分法查找
```java
//元素按从小到大排序
public static int binarysearch(int[] arry, int dest) {
    int begin = 0;//开始元素下标
    int end = arry.length - 1;//结束元素下标
    while(begin <= end){
        int mid = (begin + end)/2;
        if(arry[mid] == dest){
            return mid;
        }else if(arry[mid] < dest){
            begin = mid + 1;
        }else {
            end = mid -1;
        }
    }
    return -1;
}
```
# Arrays类
- 看帮助文档

</font>