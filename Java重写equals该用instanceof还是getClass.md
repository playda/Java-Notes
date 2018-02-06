网上流行的getClass()版本如下：
~~~
public class Student {
    private String name;

    @Override
    public boolean equals(Object object){
        if (object == this)
            return true;
        // 使用getClass()判断对象是否属于该类
        if (object == null || object.getClass() != getClass())
            return false;
        Student student = (Student)object;
        return name != null && name.equals(student.name);
}
~~~
网上流行instanceof 版本如下：
~~~
public class Student {
    private String name;

    @Override
    public boolean equals(Object object){
        if (object == this)
            return true;
        // 通过instanceof来判断对象是否属于类
        if (object == null || !(object instanceof Student))
            return false;
        Student student = (Student)object;
        return name!=null && name.equals(student.name);
    }
}
~~~
事实上两种方案都是有效的，区别就是getClass()限制了对象只能是同一个类，而instanceof却允许对象是同一个类或其子类。而问题就出在子类上，再看看下面这个instanceof 版本：
~~~
public class Student {
    private String name;

    @Override
    public boolean equals(Object object){
        if (object == this)
            return true;
        // 通过instanceof来判断对象是否属于类
        if (object == null || !(object instanceof Student))
            return false;
        Student student = (Student)object;
        // 跟上个instanceof版本唯一不同的是，这里使用student.getName()而不是student.name
        return name!=null && name.equals(student.getName());
    }
}
~~~
如果是将代码中`name.equals(student.name)`

替换成`name.equals(student.getName())`

或是替换成`getName().equals(student.name)`

第二个instanceof版本严格上来说已经不规范了，你就会发现如果 Student 有子类 GoodStudent 的话，会出现以下问题:
~~~
GoodStudent son = new GoodStudent();
Student father = new Student();
son.setName("test");
father.setName("test");

// 采用 name.equals(student.getName())
// 结果输出 son.equals(father) false
System.out.println("son.equals(father) "+son.equals(father));
// 结果输出 father.equals(son) true
System.out.println("father.equals(son) "+father.equals(son));

// 采用 getName().equals(student.name)
// 结果输出 son.equals(father) true
System.out.println("son.equals(father) "+son.equals(father));
// 结果输出 father.equals(son) false
System.out.println("father.equals(son) "+father.equals(son));
~~~
原因很简单，**name属性是private修饰的**，属于类可见范围。

而当main方法在父类中进行时：
* 如果equals方法中采用代码`name.equals(student.name)`

  假如equals的调用者是Student类，而参数student如果是GoodStudent类，由于name属性对其它类不可见，不论是否有值，student.name为null，所以equals返回false。

* 如果equals方法中采用代码`getName().equals(student.name)`

  如果equals调用者是Student类，参数是GoodStudent类，那么student.name为null，返回false。而如果调用者是GoodStudent，参数是Student，由于GoodStudent获取name属性是通过getName()方法而不是直接调用name，所以能进行正常判断。

其它情况均可按如上方法判断，综上所述，如果equals方法采用如下代码`getName().equals(student.getName())`时，这样equals方法就变成了父类与子类也可进行equals操作了，只要name属性的值一样。按照各自的业务可以有各自的equals实现，**但是按照规范来说，equals不应该是父类与子类对象可以进行的操作，所以重写equals方法应当选用getClass()来进行判断对象是否属于类，而不应该用instanceof，后者会让子类钻漏洞。**
