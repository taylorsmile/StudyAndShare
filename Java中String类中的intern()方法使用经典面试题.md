### Java中String类中的intern()方法使用经典面试题

```Java

public static void main(String[] args) {
    String s1 = new String("1");
    s1.intern();
    String s2 = "1";
    System.out.println(s1 == s2);
 
    String s3 = new String("1") + new String("1");
    s3.intern();
    String s4 = "11";
    System.out.println(s3 == s4);
}

答案：
    jdk6：
    上面两个输出的结果分别是 false, false
    jdk7/8：
    上面两个输出的结果分别是 false, true

```
***

**解决以上的代码问题，要考虑到 jdk6 和 jdk7/8 下 intern 的区别**

```
第一个输出的结果两个jdk版本都是false
原因：
    String s1 = new String("1");执行完后会创建两个对象，一个是new的对象，一个是常量池中的"1"
    jdk6 和 jdk7/8：中 在调用intern方法时，字符串常量池中已经有"1"了 （jdk6的常量池是在永久态中的，7开始都是放在堆内存中了）
    所以，s1是指向堆内存中new出的对象，而s2是指向堆内存中常量池中的"1"对象，显然地址不一样，所以为false


第二个输出的结果jdk6版本输出的是false, jdk7/8输出的是true （重要理解为何！！！）
原因：
jdk6：
    String s3 = new String("1") + new String("1");// 最终其实s3指向的是 new String("11");
    执行完以上代码后，常量池中是没有"11"这个对象的，当调用完intern()方法后，会在常量池中创建一个"11"对象
    但此时s3还是指向堆内存中的那个new出的对象 new String("11")
    String s4 = "11"; // s4是指向上面创建出的常量池中的"11"对象
    所以，两者的地址不同，输出false
jdk7/8：
    基于上面的基础之上，执行完intern方法后，会在常量池中创建一个"11"对象，但是此时堆内存中常量池外面也有一个new String("11")对象
    此时为了不增加常量池中的长度，减少堆内存中的重复对象，常量池中的"11"对象会指向外面的new出的对象 new String("11")
    然后当执行完String s4 = "11"之后，s4指向常量池中的11对象，11对象又指向外面的 new String("11")，所以最终s4指向的地址是外面的对象的地址
    由于一开始s3就是指向new String("11")，然后分析后，s4也是指向 new String("11")。
    所以，两者地址相同，输出true
 
```