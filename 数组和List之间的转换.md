### 数组转List：
```java
1、for循环遍历插入到List中

2、使用List<T> arrayList = new ArrayList<>(Arrays.asList(array));
   
    注意：不要使用List<String> list = Arrays.asList(arrays)，
        虽说Arrays.asList方法也返回一个ArrayList对象，
        但是此对象并不是util包下的ArrayList类，而是Arrays类中的静态内部类ArrayList， 
        使用此方法返回的数组集合的大小是固定的，不可以增加删除。

3、使用Collections.addAll()方法

    List<> arrayList =new ArrayList<>(array.length);
    // addAll方法底层使用增强for循环遍历数组中的元素添加到集合list中
    Collections.addAll(arrayList, arrays); 

4、JDK1.8开始的新特性Stream流的概念 （推荐）
        
  /**
   * 涉及的知识点：流式自动装箱，自动转成List
   * Arrays.stream(arr) 将int类型的数组转换成int类型的流-IntStream
   * 调用其boxed方法，将其int基本数据类型装箱成Integer对象
   * 此处的boxed方法 == mapToObj(Integer::valueOf) Integer::valueOf这是int的装箱操作
   * 再调用collect(Collectors.toList())方法将其转成List集合
   */
  
   int[] arr = {1, 10, 0};
   List list = Arrays.stream(arr).boxed().collect(Collectors.toList());
   // List list = Arrays.stream(arr).mapToObj(Integer::valueOf).collect(Collectors.toList());
   for (int i = 0; i < list.size(); i++) {
       System.out.print(list.get(i) + " ");
   }
```

### List转数组：
```java
1、遍历List，创建新数组

2、list.toArray(T[] a)方法创建数组

    // 使用有参数的toArray(T[] a)方法，可以根据list的大小，创建指定大小的数组
    Integer[] arr = list.toArray(new Integer[list.size()]);
    
    /** 此时Integer对象数组创建好了，现在需要int基本类型的数组
     * 则继续使用java8的新特性流的mapToInt(xxx)方法,xxx指定Integer的拆箱操作Integer::intValue
     */
    int[] newArr = Arrays.stream(arr).mapToInt(Integer::intValue).toArray();

3、直接通过Stream流式将list转成int数组 (推荐)
        
   int[] nums = list.stream().mapToInt(Integer::intValue).toArray();
```

### 扩展
像上面提到的Stream流式处理数值的方法，常用的还有很多，比如：

找出某集合或者某数组中的最小值或者最大值；排序等

数组中的最大和最小：
```java
Arrays.stream(arr).max().getAsInt();
Arrays.stream(arr).min().getAsInt();
```

数组排序：
```java
// 数组中是正序排序的
Arrays.stream(arr).sorted().toArray(); 
```

集合中的最大和最小：
```java
/**
 * list的流式max和min方法是有参数的，这个参数就是比较器，可以使用集合类的数据类型的比较器
 */
list.stream().max(Integer::compareTo).get();
list.stream().min(Integer::compareTo).get();

```
