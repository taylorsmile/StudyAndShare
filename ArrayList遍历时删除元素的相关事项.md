### 第一种方法：普通for循环正序删除 （结果：会漏掉元素判断）
```Java
for (int i = 0; i < arrayList.size(); i++) {
    if (arrayList.get(i) == 3) {
        arrayList.remove(i);
    }
    System.out.println(arrayList.toString());
}

如上所示，使用ArrayList的remove方法时，后面的数据会往前移，也就是说上面的输出语句输出结果：

当前arrayList是[1, 2, 3, 3, 4, 5]

当前arrayList是[1, 2, 3, 3, 4, 5]

当前arrayList是[1, 2, 3, 4, 5]

当前arrayList是[1, 2, 3, 4, 5]

当前arrayList是[1, 2, 3, 4, 5]
```

由此可知，遍历之后，还有3；因为调用remove方法的时候，会调用System.arraycopy方法，将remove元素后的所有元素向"左移"一位，所以，出现上面的删除元素3，未删干净。总结：如果是连续的重复元素，使用以上方式遍历删除会导致少删除的现象。

解决方案：
可以在删除元素后，执行i=i-1，使得下次循环时再次对该数组下标进行判断。具体修改为：

```Java
for (int i = 0; i < arrayList.size(); i++) {
    if (arrayList.get(i) == 3) {
        arrayList.remove(i);
        i--;  //解决方案: 加一行代码i = i - 1; 删除元素后，下标减1
    }
    System.out.println(arrayList.toString());
}
```


### 第二种方法：普通for循环倒序删除 (结果：正确删除)

```Java
for (int i = arrayList.size()-1; i >= 0; i--) {
    if (arrayList.get(i) == 3) {
        arrayList.remove(i);
    }
    System.out.println(arrayList.toString());
}
```


### 第三种方法：for-each循环删除（结果：抛出异常）

```Java
/**原ArrayList是[1, 2, 3, 3, 4, 5] */
for (Integer value : arrayList) {
    if (value.equals(3)) { //3是要删除的元素
        arrayList.remove(value);
    }
}

以上遍历删除的输出结果是：

当前arrayList是[1, 2, 3, 3, 4, 5]
当前arrayList是[1, 2, 3, 3, 4, 5]
当前arrayList是[1, 2, 3, 4, 5]
Exception in thread "main" java.util.ConcurrentModificationException
    at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:901)
    at java.util.ArrayList$Itr.next(ArrayList.java:851)
    at com.test.ArrayListTest1.removeWayThree(ArrayListTest1.java:50)
    at com.test.ArrayListTest1.main(ArrayListTest1.java:24)
```
为什么会抛出ConcurrentModificationException异常(并行修改异常)：
```Java

反编译上面的这个类
方法：
javac xxxxx.java//生成具体的.class文件

javap -c xxxxx.class//对class文件进行反编译

```

通过反编译后的文件可看出，for-each循环，其实底层的实现是使用了ArrayList的iterator迭代器的的hasNext()方法和next()方法来共同实现的。iterator.hasNext()来判断是否还有下一个元素，iterator.next()方法来获取下一个元素。

```Java

当执行next方法时，会一开始执行checkForComodification方法，也就是说检查是否对arrayList集合数组进行了修改。主要是对比modCount和expectedModCount这2个变量的值。

而因为在删除元素时，remove(Object o)方法会调用fastRemove()方法，其中会对modCount+1操作，表示对数组进行了修改，所以将修改次数+1。删除元素过后，会继续调用next方法，执行checkForComodification方法检查是否修改数组了。

在上面的例子中，刚开始modCount和expectedModCount的值都为6，所以第1次和第二次获取的数值都正常。当第三次遍历的时候，由于是3，执行remove方法后，modCount+1，也就是变成了7，当下次循环，执行next方法的时候，执行checkForComodification方法，发现modCount是7，而expectedModCount是6，不等，则会抛出并行修改的异常。

```

所以for-each方法不能够实现遍历删除某元素。

解决方案：见下面一种方法。


### 第四种方法：Iterator遍历，使用Iterator的remove删除元素（结果：正确删除）

```Java
Iterator<Integer> iterator = arrayList.iterator();
while (iterator.hasNext()) {
    Integer value = iterator.next();
    if (value.equals(3)) {  //3是需要删除的元素
        iterator.remove();  
    }
}
```

由第三种法分析得出ArrayList的迭代器的Itr类中的remove方法，会对每次元素删除后，修改modCount值，后面同时会更新expectedModCount的值，这就保持了两者的值一样，从而不会在删除元素的时候抛出并行修改异常ConcurrentModificationException。

所以，直接使用迭代器的remove方法对arrayList里的元素进行删除。














