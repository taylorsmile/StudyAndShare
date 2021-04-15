```java
List<Integer> 转 int[] 的方法
        
通常每一个list都有一个toArray()的方法来转成数组，但是返回的是一个数组对象Object[]
只能是一些包装类性，比如Integer[]
        
例：Integer[] n = (Integer[]) list.toArray();

但是想要转成int[]基本数据类型的数组是不可以强转的

这里有两种方法：**** jdk1.8新特性  通过流式stream处理 ****

```

```java
方法一：

*******基于上面已经转成包装类类型的Integer[], 来转成int[]类型******* 
        
想要转成int[]类型, 先转成IntStream类型，然后再调用其toArray方法转成int[]
        
int[] nums = Arrays.stream(n).mapToInt(Integer::intValue).toArray(); 


        
方法二：
        
*******可以直接将List的类型转成int[]数组*******  

同样的List类型先转成IntStream类型, 然后再调用其toArray方法转成int[]
        
int[] nums = list.stream().mapToInt(Integer::intValue).toArray();

```