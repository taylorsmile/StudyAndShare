## 通过反射创建私有构造器实例对象

* 若一个类的构造方法设置为private，然后可以通过反射来创建其实例

### 通过反射创建实例有两种方式：getConstructor() 和 getDeclaredConstructor()方法

```Java
通过测试总结：

当某个类的构造方法是public的，这两种方式都可以，如下代码：

Class<Person> person = Person.class;
// Class<Person> person = Class.forName("com.study.dao.Person");
Constructor<Person> constructor = person.getConstructor();
Person obj = constructor.newInstance();

Class<Person> person = Person.class;
// Class<Person> person = Class.forName("com.study.dao.Person");
Constructor<Person> constructor = person.getDeclaredConstructor();
Person obj = constructor.newInstance();


如果当某个类的构造方法是private的，getConstructor方法是不可用的，
getDeclaredConstructor方法可以使用，但是必须要配合setAccessible(true)方法使用才行
代码如下：

Class<Person> person = Person.class;
// Class<Person> person = Class.forName("com.study.dao.Person");
Constructor<Person> constructor = person.getDeclaredConstructor();
constructor.setAccessible(true);
Person obj = constructor.newInstance();

```

### 总结

若想通过反射来创建一个私有化private构造器类的对象时，一定要用getDeclaredConstructor()方法并设置构造器可访问setAccessible(true)
才可以创建其实例对象。