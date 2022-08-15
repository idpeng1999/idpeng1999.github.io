# 基础知识整理

## ==、Equals区别？

1. 对象类型不同

* equals()：是超类Object中的方法。
* ==：是操作符。

2. 比较的对象不同

* equals()：用来检测两个对象是否相等，即两个对象的内容是否相等。
* ==：用于比较引用和比较基本数据类型时具有不同的功能

3. 运行速度不同

* equals()：没有==运行速度快。
* ==：运行速度比equals()快，因为==只是比较引用。

## Continue、break和return的区别是什么？

在循环结构中，当循环条件不满足或者循环次数达到要求时，循环会正常结束。
但是，有时候可能需要在循环的过程中，当发生了某种条件之后 ，提前终止循环，
这就需要用到下面几个关键词：

* `continue`：指跳出当前的这一次循环，继续下一次循环。
* `break`：指跳出整个循环体，继续执行循环下面的语句。

`return`：用于跳出所在方法，结束该方法的运行。return 一般有两种用法：

* `return;` ：直接使用 return 结束方法执行，用于没有返回值函数的方法
* `return value;` ：return 一个特定值，用于有返回值函数的方法

下列语句的运行结果是什么？

```java
 public static void main(String[] args) {
        boolean flag = false;
        for (int i = 0; i <= 3; i++) {
            if (i == 0) {
                System.out.println("0");
            } else if (i == 1) {
                System.out.println("1");
                continue;
            } else if (i == 2) {
                System.out.println("2");
                flag = true;
            } else if (i == 3) {
                System.out.println("3");
                break;
            } else if (i == 4) {
                System.out.println("4");
            }
            System.out.println("xixi");
        }
        if (flag) {
            System.out.println("haha");
            return;
        }
        System.out.println("heihei");
    }
```
运行结果:

```
0
xixi
1
2
xixi
3
haha
```

## Drop、delete区别？

### 用法不同

* `drop`(丢弃数据):`drop table`表名 ，直接将表都删除掉，在删除表的时候使用。
* `delete`（删除数据）:`delete from 表名 where 列名=值`，删除某一列的数据

---

* 不带`where`子句的`delete`、以及`drop`都会删除表内的数据
* 但是`delete`只删除数据不删除表的结构(定义)
* 执行`drop`语句，此表的结构也会删除，也就是执行`drop`之后对应的表不复存在。

## Final的作用？

在Java中，final关键字可以用来修饰类、方法和变量（包括成员变量和局部变量）。

1. 修饰类

当用final修饰一个类时，表明这个类不能被继承。

2. 修饰方法

如果只有在想明确禁止,该方法在子类中被覆盖的情况下才将方法设置为final的。
* 即父类的final方法是不能被子类所覆盖的，也就是说子类是不能够存在和父类一模一样的方法的。

3. 修饰变量

* final成员变量表示常量，只能被赋值一次，赋值后值不再改变。
* 当final修饰一个基本数据类型时，表示该基本数据类型的值一旦在初始化后便不能发生变化；
* 如果final修饰一个引用类型时，则在对其初始化之后便不能再让其指向其他对象了，但该引用所指向的对象的内容是可以发生变化的。
* final修饰一个成员变量（属性），必须要显示初始化。
* 当函数的参数类型声明为final时，说明该参数是只读型的。即你可以读取使用该参数，但是无法改变该参数的值。

## Java异常

## 异常基本类型

* 异常类的基本类型是`Throwable`类
* 两大子类分别是`Error`和`Exception`

### Error

* 系统错误由Java虚拟机抛出，用`Error`类表示。`Error`类描述的是内部系统错误
* 例如：Java虚拟机崩溃。在程序中不会对`Error`异常进行捕捉和抛出。

### Exception

异常`Exception`又分为`RuntimeException`(运行时异常)和`CheckedException`(检查时异常)

#### RuntimeException(运行时异常)

* 程序运行过程中才可能发生的异常,一般为代码的逻辑错误
* 例如：类型错误转换，空指针异常、找不到指定类等

#### CheckedException(检查时异常)

* 编译期间可以检查到的异常，必须显式的进行处理（捕获或者抛出到上一层）
* 例如：`IOException`, `FileNotFoundException`,`SQLException`等

## 异常处理

### throws（声明异常）

* 在方法头中显式声明该异常，以便于告知方法调用者此方法有异常
* 若父类的方法没有声明异常，则子类继承方法后，也不能声明异常

### try-catch（捕获异常）

* 若执行`try`块的过程中没有发生异常，则跳过`catch`子句
* 若是出现异常，`try`块中剩余语句不再执行。
* 再判断`catch`块的异常类是否是捕获的异常类型，匹配后执行相应的`catch`块中的代码。
* 如果有`finally`的话进入到`finally`里面继续执行。
* `try ctach fianally`中有`return`时，会先执行`return`,但是不会返回。在执行完`finally`后进行返回
* `catch`语句可以有一个或多个，`finally`语句最多一个

## throw,throws关键字区别

* `throw`关键字是用于方法体内部，用来抛出一个Throwable类型的异常
* `throws`关键字用于方法体外部的方法声明部分，用来声明方法可能会抛出某些异常

```java
public static void test()throws Exception{
    throw new Exception("方法test中的Exception");
}  
```

## 常见的几种异常

RuntimeExceptionjava.lang.ArithmeticException(算术异常)
<br>

* java.lang.`NullPointerException`(空指针异常)
* java.lang.`ClassCastException`(类型转换异常)
* java.lang.`IllegalArgumentException`(不合法的参数异常)
* java.lang.`IndexOutOfBoundsException`(数组下标越界异常)

<br>

java.io.IOException(IO流异常)
<br>

* java.lang.`ClassNotFoundException`(没找到指定类异常)
* java.lang.`NoSuchFieldException`(没找到指定字段异常)
* java.lang.`NoSuchMetodException`(没找到指定方法异常)
* java.lang.`IllegalAccessException`(非法访问异常)
* java.lang.`InterruptedException`(中断异常)

## Java的参数传递是传值还是传引用？

* Java世界中的一切对象都是指针(地址)
* 函数调用永远是传值

1. 基本类型（包括String类）作为参数传递时，是传递值的拷贝，无论你怎么改变这个拷贝，原值是不会改变的
2. 引用类型（包括数组，对象以及接口）作为参数传递时，是把对象在内存中的地址拷贝了一份传给了参数。
* 注意：基本数据类型的封装类Integer、Short、Float、Double、Long、Boolean、Byte、Character虽然是引用类型，但它们在作为参数传递时，也和基本数据类型一样，是值传递。

## Java的基本类型

基本数据类型是CPU可以直接进行运算的类型。Java定义了以下几种基本数据类型：

* 整数类型：byte，short，int，long
* 浮点数类型：float，double
* 字符类型：char
* 布尔类型：boolean

String是基本数据类型吗？答：**不是**

![基本类型](/img/Java的基本类型/1.png)

## Java面向对象三大特性封装继承多态

### 封装

* 封装把⼀个对象的属性私有化，同时提供⼀些可以被外界访问的属性的⽅法

### 继承

* 继承是使⽤已存在的类的定义作为基础建⽴新类的技术，新类的定义可以增加新的数据或新的功能,
* 也可以⽤⽗类的功能，但不能选择性地继承⽗类。通过使⽤继承我们能够⾮常⽅便地复⽤以前的代码。

### 多态

* 多态是指程序中定义的引⽤变量所指向的具体类型和通过该引⽤变量发出的⽅法调⽤在编程时并不确定，⽽是在程序运⾏期间才确定，
* 即⼀个引⽤变量到底会指向哪个类的实例对象，该引⽤变量发出的⽅法调⽤到底是哪个类中实现的⽅法，必须在由程序运⾏期间才能决定。
  <br>
  <br>
* 在 Java 中有两种形式可以实现多态：继承（多个⼦类对同⼀⽅法的重写）和接⼝（实现接⼝并覆盖接⼝中同⼀⽅法）。



## Object有哪些方法？

* Object类，属于java.lang包，位于类层次结构树的顶部。

* 每个类都是Object类的直接或间接的后代。

* 使用或编写的每个类都继承Object的实例方法。


常用方法：

* `getClass 方法` final 方法、获取对象的运行时 class 对象，class 对象就是描述对象所属类的对象。
* `hashCode 方法` 该方法主要用于获取对象的散列值。Object 中该方法默认返回的是对象的堆内存地址。
* `equals 方法` 该方法用于比较两个对象，如果这两个对象引用指向的是同一个对象，那么返回 true，否则返回 false。
* `clone 方法` 该方法是保护方法，实现对象的浅复制，只有实现了 Cloneable 接口才可以调用该方法，否则抛出 CloneNotSupportedException 异常。
* `toString 方法` 返回一个 String 对象，一般子类都有覆盖。默认返回格式如下：对象的 class 名称 + @ + hashCode 的十六进制字符串。
* `wait 方法` 当timeout 为 0，即不等待。

## StringBuffer、StringBuilder区别?

### 区别

* StringBuffer稍慢，但线程安全
* StringBuilder更快，但线程不安全，常用

### 线程安全性

* 如果没有额外声明，所有类的默认都是线程不安全的

## String中有哪些方法？

常用方法：

* `indexOf()` 返回指定字符的索引。
* `charAt()` 返回指定索引处的字符。
* `replace()` 字符串替换。
* `trim()` 去除字符串两端空白。
* `split()` 分割字符串，返回一个分割后的字符串数组。
* `getBytes()` 返回字符串的 byte 类型数组。
* `length()` 返回字符串长度。
* `toLowerCase()` 将字符串转成小写字母。
* `toUpperCase()` 将字符串转成大写字符。
* `substring()` 截取字符串。
* `equals()` 字符串比较。

## 为什么重写equals时必须重写hashCode方法？

* 因为两个相等的对象的`hashCode`值必须是相等。
* 也就是说如果`equals`方法判断两个对象是相等的，那这两个对象的`hashCode`值也要相等。
* 如果重写`equals()`时没有重写`hashCode()`方法的话就可能会导致`equals`方法判断是相等的两个对象,`hashCode`值却不相等。

## 什么时候用接口或抽象类？

1. 如果预计要创建组件的多个版本，则创建`抽象类`。抽象类提供简单的方法来控制组件版本；
2. 如果创建的功能将在大范围的全异对象间使用，则使用`接口`。如果要设计小而简练的功能块，则使用接口；
3. 如果要设计大的功能单元，则使用`抽象类`。如果要在组件的所有实现间提供通用的已实现功能，则使用抽象类；
4. `抽象类`主要用于关系密切的对象；而`接口`适合为不相关的类提供通用功能。

## 重写和重载的作用？

* `override`是重写,重写是一种动态绑定的多态机制。
* `overload`是重载，重载是一种参数多态机制，即代码通过参数的类型或个数不同而实现的多态机制。

`@Override`是伪代码,表示`重写`(当然不写也可以)，不过写上有如下好处:

1. 可以当注释用,方便阅读；
2. 编译器可以给你验证@Override下面的方法名是否是你父类中所有的，如果没有则报错。

![比较](/img/重写和重载的作用？/1.png)

## 静态方法为什么不能调用非静态方法？

1. 静态方法是属于类的，在类加载的时候就会分配内存，可以通过类名直接访问。而非静态成员属于实例对象，只有在对象实例化之后才存在，需要通过类的实例对象去访问。
2. 在类的非静态成员不存在的时候静态成员就已经存在了，此时调用在内存中还不存在的非静态成员，属于非法操作。

## 静态方法和实例方法有何不同？\

### 调用方式

* 在外部调用静态方法时，可以使用`类名.方法名`的方式，也可以使用`对象.方法名`的方式，而实例方法只有后面这种方式。也就是说，**调用静态方法可以无需创建对象**。
* 不过，需要注意的是一般不建议使用`对象.方法名`的方式来调用静态方法。这种方式非常容易造成混淆，静态方法不属于类的某个对象而是属于这个类。

因此，一般建议使用`类名.方法名`的方式来调用静态方法。

```java
public class Person {
    public void method() {
      //......
    }

    public static void staicMethod(){
      //......
    }
    
    public static void main(String[] args) {
        Person person = new Person();
        // 调用实例方法
        person.method();
        // 调用静态方法
        Person.staicMethod()
    }
}
```

### 访问类成员是否存在限制

* 静态方法在访问本类的成员时，只允许访问静态成员（即静态成员变量和静态方法），不允许访问实例成员（即实例成员变量和实例方法），而实例方法不存在这个限制。

## 深拷贝、浅拷贝的区别？

* 浅拷贝是按位拷贝对象，它会创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。
  简而言之，浅拷贝仅仅复制所考虑的对象，而不复制它所引用的对象。
* 深拷贝会拷贝所有的属性,并拷贝属性指向的动态分配的内存。当对象和它所引用的对象一起拷贝时即发生深拷贝。深拷贝相比于浅拷贝速度较慢并且花销较大。
  简而言之，深拷贝把要复制的对象所引用的对象都复制了一遍。

## 自增运算符++和自减运算符

* ++ 和 -- 运算符可以放在变量之前，也可以放在变量之后
* 当运算符放在变量之前时(前缀)，先自增/减，再赋值 `++a`
* 当运算符放在变量之后时(后缀)，先赋值，再自增/减 `a--`

例如

* 当 b = ++a 时，先自增（自己增加 1），再赋值（赋值给 b）
* 当 b = a++ 时，先赋值(赋值给 b)，再自增（自己增加 1）

* ++a 输出的是 a+1 的值，a++输出的是 a 值
* **“符号在前就先加/减，符号在后就后加/减”**

## 接口和抽象类有什么区别or联系？

* Interface(接口):定义功能，只能包含方法(实现),不能包含成员变量，可以被实现若干次。
* Abstract class(抽象类):定义抽象的骨架实现，可以包含抽象方法或者实现，也可以包含成员变量，只能沿着一条路径继承。



