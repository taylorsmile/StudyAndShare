### StringJoiner也是字符串的拼接类，但是对StringBuilder类做了一些拼接的升级


普通的StringBuilder拼接字符串对象时，若中间需要将每个字符串通过逗号隔开，则需要在每次的拼接后面手动添加逗号，比如：sb.append(i + "，");
在StringJoiner类中提供了两个构造函数，都是通过add函数拼接的对象，分别是
```
/**
 * 此构造函数只需要提供拼接时每个对象间的分隔符就行，其余参数便是默认为空
 *
 */
public StringJoiner(CharSequence delimiter) {
        this(delimiter, "", "");
    }
e.g.  StringJoiner sj = new StringJoiner(",")
      sj.add("1").add("2");   // 1,2
      
/**
 * 此构造函数需要提供拼接时每个对象间的分隔符就行，和这个拼接后的字符串的前置符号和后置符号
 *
 */
public StringJoiner(CharSequence delimiter,
                        CharSequence prefix,
                        CharSequence suffix)

e.g.  StringJoiner sj = new StringJoiner(",", "{", "}")
      sj.add("1").add("2");   // {1,2"}
```