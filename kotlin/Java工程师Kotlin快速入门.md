# Java工程师Kotlin快速入门

本文列举了Java与Kotlin在语法上的不同以及Kotlin语法糖，以方便Java工程师快速入门。

[TOC]

## 变量与函数

本小节介绍Kotlin中变量与函数的声明，以及if、when、for等流程控制语句。



### 变量的声明方式

kotlin中声明变量只允许两种关键字，且自带类型推导

+ var 声明变量 值可变
+ val 声明常量  值不可变 相当于Java中的final关键字

```kotlin
var a: Int = 10
val b = 10
```

Java和Kotlin数据类型对照表

| Java基本数据类型 | Kotlin对象数据类型 | 数据类型说明 |
| ---------------- | ------------------ | ------------ |
| int              | Int                | 整型         |
| long             | Long               | 长整型       |
| short            | Short              | 短整型       |
| float            | Float              | 单精度浮点型 |
| double           | Double             | 双精度浮点型 |
| boolean          | Boolean            | 布尔型       |
| char             | Char               | 字符型       |
| byte             | Byte               | 字节型       |



### 函数的组成

在Kotlin中，函数由以下部分组成：

- **fun**：关键字用于声明一个函数。
- **functionName**：函数的名称，用于标识函数。
- **parameterName**：参数的名称，用于在函数内部引用参数。
- **ParameterType**：参数的类型，指定参数的数据类型。
- **ReturnType**：返回类型，指定函数返回的数据类型。
- **函数体**：花括号内包含函数的实际操作，包括函数的具体实现。

```kotlin
fun functionName(parameterName: ParameterType): ReturnType {
    // 函数体
    // 可以包含函数的具体实现
}
```

但是如果函数只有一句就可以精简为以下的形式：

```kotlin
// 等于号已经说明了函数的结果
fun largerNumber(num1: Int, num2: Int): Int = max(num1, num2)
```

```kotlin
// 进一步的推导
fun largerNumber(num1: Int, num2: Int) = max(num1, num2)
```



### 字符串模板

Kotlin自带了字符串模板，避免了我们在输出某些信息的时候需要通过+号拼接。

```kotlin
fun main() {
    val name = "XuanRan"
    println("Hello $name!")
}
```

输出结果：Hello XuanRan!





#### 函数参数默认值

```kotlin
fun printParams(num: Int, str: String = "hello") {
 println("num is $num , str is $str")
}
```



### 条件语句

```kotlin
// 仿照Java的条件语句写法
fun maxValue(i:Int, k:Int) :Int {
    if (i > k) {
        return i
    }else{
        return k
    }
}
```

```kotlin
// Kotlin简化写法
fun maxValue(i:Int, k:Int) :Int {
    val res = if (i > k) {
        i
    }else{
         k
    }
    return res
```
其中的return 可省略
```kotlin
// 进一步Kotlin简化写法
fun maxValue(i:Int, k:Int) = if (i > k) {i} else {k}
```

```kotlin
// kotlin 结合函数后的进一步写法
fun maxValue(i:Int, k:Int) = if (i > k) i else k
```



### when条件语句

when语句相当于Java中的switch语句，同样在此处的return是可省略的。

```kotlin

fun getScope(name: String) = when(name) {
    "Tim" -> 90
    "Tom" -> 100
    else -> 1
}
```



### 循环语句

+ 基本循环语句

```kotlin
// 最基本的for
fun oneToTen() {
    for(i in 0..10){
        println(i)
    }
}
```

`..`用于形成一个[0,10]的区间，相当于Java的以下写法：

```java
private void oneToTen() {
    for (int i = 0; i <= 10 ; i++) {
        System.out.println(i);
    }
 }
```

+ for-in区间遍历

```kotlin
var r = 0 until 10
```

上述代码表示创建了一个0到10的左闭右开区间，它的数学表达方式是[0, 10)。

+ step 步长

```kotlin

fun oneToTen() {
    for( i in 0 until 10 step 2) {
        println(i)
    }
}
```

上述代码表示在遍历[0, 10)这个区间的时候，每次执行循环都会在区间范围内递增2

+ downTo遍历降序区间

```kotlin
// 创建了一个[10, 1]的降序区间
fun downTo() {
    for(i in 10 downTo 1) {
        println(i)
    }
}
```





## 类与对象

本小节介绍Kotlin中类与对象。



### 类的组成

Kotlin中类的基本组成可写成以下的形式，Kotlin也是使用class关键字声明一个类的，我在此也同时加入了name和age两个字段。

```kotlin
class Student: Person() {
    var name = ""
    var age = 1

    fun eat() {
        println("$name is $age")
    }
}
```

Kotlin中类的实例化并不需要new关键字

```kotlin
var student = Student()
```



#### 继承与构造函数

+ 主构造函数

设我们现在有一个学生类与人类，根据关系学生属于人，应该继承人，但是Kotlin与Java并不同，Kotlin中非抽象类不可实现，就像Java中加了final关键字的类一样，如果要使Student继承Person我们需要在class前使用`open`关键字来告诉kotlin。

```kotlin
open class Person {
    var age = 0
}
```

Java中继承使用extends关键字，但是在Kotlin中需要这样写，且Persion需要加上括号，下文讲到。

```kotlin
class Student: Person() {
    var name = ""
 }
```



Kotlin还涉及主构造函数和次构造函数的区别，主构造函数将会是你最常用的构造函数，每个类都默认有一个不带参数的构造函数，这点与Java类似，你也可以显式的指定参数，主构造函数的特点是没有函数体，直接写在类名后面即可，但你可能会想如果没有函数体我想在初始化的时候执行一些操作怎么办，这时候我们可以使用init结构体，类似于Java中的static结构体：

```kotlin
class Student(val name: String,val age: Int): Person() {
    init {
        println("$name is $age")
    }
}
```

```kotlin
fun main() {
    var s = Student("a",1)
}
```

这样我们就创建了一个Student对象


那么Person的括号到底是干什么用的？实际上那个括号主要是为了选择构造函数，在Java中子类在构造函数中需要调用父类的构造函数，这个规定在Kotlin也是一样的，只不过Kotlin是子类的主构造函数调用父类中的哪个构造函数，在继承的时候通过括号来指定，看个代码就懂了。

我们修改Person的主构造函数为以下内容：

```kotlin
open class Person(name: String, age: Int) {
}
```

此时，继承Person的子类Student开始报错了：
![image-20240802163004432](E:\桌面文件夹\知识\Kotlin\知识.assets\image-20240802163004432.png)

解决办法就是我们再给Student增加参数：

```kotlin
class Student(val name: String, val age: Int, name2: String, age2: Int): Person(name2, age2) {
    init {
        println("$name is $age")
    }
}
```

> 注意，我们在Student类的主构造函数中增加name2和age2这两个字段时，不能再将它们声明成 val，因为在主构造函数中声明成val或者var的参数将自动成为该类的字段，这就会导致和父 类中同名的name2和age2字段造成冲突。因此，这里的name2和age2参数前面我们不用加任何关键字，让它的作用域仅限定在主构造函数当中即可。



+ 次构造函数

任何一个类只能有一个主构造函数，但是可以有多个次构造函数。次构造函数也可 以用于实例化一个类，这一点和主构造函数没有什么不同，只不过它是有函数体的。

Kotlin规定，当一个类既有主构造函数又有次构造函数时，所有的次构造函数都必须调用主构造 函数（包括间接调用），这部分也不难，举个例子：

```kotlin
class Student(val name: String, val age: Int, name2: String, age2: Int): Person(name2, age2) {
    init {
        println("$name is $age")
    }

    constructor(name: String, age: Int): this(name, age, "name2", 2) {
        
    }

    constructor(): this("XuanRan", 18) {

    }
}
```

次构造函数使用constructor来声明，这里我们定义两个，从上往下数，第二个通过this调用第一个构造函数，第一个构造函数通过this调用主构造函数。

此时，这三个构造函数均能正常使用。



+ 无主有次

有时候我们会出现一种情况，无主构造函数有次构造函数，这种情况在Kotlin也是允许的，但是基本见不到，在这种情况下，我们可以省略括号的写法，并使用super关键字，这点就和Java类似了：

```kotlin
class Student: Person {
    constructor(name: String, age: Int): super(name, age) {

    }
}
```



### 数据类

我们有时候在Java开发中需要经常重写equals、hashCode、toString方法，但是Kotlin提供了一个修饰符能够自动的帮我们完成，那就是数据类，使用data修饰。

```kotlin
data class Phone(val brand: String, val price: Double)
```

他的效果等于Java中的

```java
public class Phone {
    private String brand;
    private double price;

    public Phone(String brand, double price) {
        this.brand = brand;
        this.price = price;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Phone phone = (Phone) o;
        return Double.compare(phone.price, price) == 0 && Objects.equals(brand, phone.brand);
    }

    @Override
    public int hashCode() {
        return Objects.hash(brand, price);
    }

    @Override
    public String toString() {
        return "Phone{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                '}';
    }
}

```

### 单例类

在Java中单例类，我们需要通过一个静态变量来存储类对象，并且在获取实例的时候进行判断是否已初始化，如果已经初始化直接返回，否则创建新对象后返回，这种一成不变的套路在Kotlin可以直接通过一个object修饰符来完成。

```kotlin
object Phone {
    fun hello() {
        println("Hello!")
    }
}
```

```kotlin
fun main() {
    // 直接像调用静态方法那般调用即可
    Phone.hello()
}
```

### 密封类

在Kotlin中，假设接口类中没有方法，那么可以直接省略大括号。这里我们定义两个Result的实现类，Success与Error

```kotlin
interface Result
class Success(val msg : String) : Result
class Error(val error : Exception) : Result
```

假设我们有一个方法需要根据Result来获取返回的信息，但是由于when的语法规则中必须存在默认的else分支，我们不得不需要编写else的代码：

```kotlin
fun getResultMsg(result: Result) = when(result) {
    is Success -> result.msg
    is Error -> result.error.message
    else -> throw RuntimeException()
}
```

并且最重要是，假设以后Result多了更多的实现，我们可能会忘记在方法中添加其他的实现造成抛出异常，给他而密封类就可以很好的解决这个问题，密封类的关键词是`sealed class`，我们修改Result的代码：

```kotlin
sealed class Result
class Success(val msg : String) : Result()
class Error(val error : Exception) : Result()
```

由于密封类是可以被继承的，所以在继承时需要加一个括号，然后我们修改getResultMsg的方法时就发现else是可以忽略的：

```kotlin
fun getResultMsg(result: Result) = when(result) {
    is Success -> result.msg
    is Error -> result.error.message
}
```

并且假设Result类增加了新的继承，when这里会报错提示我们必须要考虑到新的情况。

### 类型强转

在Java中，我们可以通过`(转换类型)`来实现类型的强制转换，但是在Kotlin中，我们就需要使用`as`关键字进行类型转换：

```kotlin
// 将student这个变量强转成Study
var s : Study = student as Study
```



## 接口

与Java一致，Kotlin的接口也是使用interface修饰，与Java不同的是Kotlin支持默认实现，而Java自JDK1.8之后才支持默认实现。

```kotlin
interface Study {
    fun doHomeWork()

    fun doReadBook() {
        println("read book.")
    }
}
```

接口的实现在Java中是implements，在Kotlin中则是冒号，然后多实现使用逗号隔开。

```kotlin
class Student(name: String, age: Int, val clazz: String): Person(name, age), Study {
    override fun doHomeWork() {
        println("do homework.")
    }
}
```

我们在此节补充一下Java和Kotlin中可见性修饰符。

| 修饰符    | Java                               | Kotlin             |
| --------- | ---------------------------------- | ------------------ |
| public    | 所有类可见                         | 所有类可见（默认） |
| private   | 当前类可见                         | 当前类可见         |
| protected | 当前类、子类、同一包路径下的类可见 | 当前类、子类可见   |
| default   | 同一包路径下的类可见（默认）       | 无                 |
| internal  | 无                                 | 同一模块中的类可见 |





## 集合与Lambda

### 集合

ArrayList给我们提供了一个add方法添加元素，但是在Kotlin使用lambda会简洁很多。

```kotlin
// 不使用lambda
fun main() {
    var res = ArrayList<String>()
    res.add("1")
    res.add("2")
    res.add("3")
}
```

```kotlin
// lambda
fun main() {
    var res = listOf("1", "2", "3")
}
```

使用listOf时需要注意，其产生的集合是不可变的，如果需要创建可变集合需要使用mutableListOf



### Map

Kotlin中的Map写法：

```kotlin
// 不使用lambda
fun main() {
    var map = HashMap<String, Int>()
    map["a"] = 1
    map["b"] = 2
}
```

```kotlin
// lambda
fun main() {
    var map = mapOf("a" to 1, "b" to 2)
}
```

```kotlin
// 遍历输出
fun main() {
    var map = mapOf("a" to 1, "b" to 2)
    for ((key, value) in map) {
        println("$key is $value")
    }
}
```



### 函数式API

假设我们有一个水果集合。

```kotlin
fun main() {
    val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon")
}
```

我需要求得这个集合里面单词最长的元素，你可能会想到以下代码：

```kotlin
fun main() {
    val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon")
    var maxWord = "";
    for (s in list) {
        if (s.length > maxWord.length) maxWord = s
    }
    println("max length : $maxWord" )
}
```

这样确实能做到，但是在Kotlin中我们可以使用函数式API来让它变得更加精简，它与Java的Stream十分相似。

```kotlin
fun main() {
    val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon")
    val maxWord = list.maxBy { m -> m.length }
    println("max length : $maxWord")
}
```

其中Lambda表达式的语法结构为：

```txt
{参数名1: 参数类型, 参数名2: 参数类型 -> 函数体}
```

其实上面提到的maxBy就是一个普通的函数，只不过它接收的是一个Lambda的参数而已，所以我们可以将maxBy的参数单独提取出来：

```kotlin
fun main() {
    val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon")
    val la = {m : String -> m.length}
    val maxWord = list.maxBy(la)
    println("max length : $maxWord")
}
```

此外，Kotlin规定如果Lambda是函数的最后一个参数，可以将Lambda表达式移到函数括号外面，且如果Lambda是唯一一个参数时，函数参数括号可省略：

```kotlin
fun main() {
    val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon")
    val maxWord = list.maxBy { m -> m.length }
    println("max length : $maxWord")
}

```

并且如果Lambda只有一个参数的话(上文代码中的m)，其参数可以省略并使用`it`代替。

```kotlin
fun main() {
    val list = listOf("Apple", "Banana", "Orange", "Pear", "Grape", "Watermelon")
    val maxWord = list.maxBy { it.length }
    println("max length : $maxWord")
}
```



#### Java函数API

这是Java中Runnable接口的源码，它只有一个方法，在Java中Lambda可以写成如下形式：

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}

```

```java
    public static void main(String[] args) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("Hello");
            }
        }).start();
    }
```

但是在Kotlin中，这些代码可以再精简一些，由于Kotlin舍弃了new关键字，所以我们在Kotlin中创建匿名内部类需要使用`object`关键字。

```kotlin
fun main() {
    Thread(object : Runnable{
        override fun run() {
            println("Hello!")
        }
    }).start()
}
```

因为Runnable接口只有一个run方法，所以我们不写函数名称Kotlin也是知道我们的意思就是要实现run方法，这段代码可以再精简一点：

```kotlin
fun main() {
    Thread(Runnable {
        println("Hello!")
    }).start()
}
```

不过到这里还没结束，假设Java方法的参数列表中有且仅有一个Java单抽象方法接口参数，那么接口名也是可以省略的，并且按照我们之前提到的，假设Lambda表达式是方法参数的最后一个参数时，实现体可以放在方法括号后面，所以最终被精简的代码如下：

```kotlin

fun main() {
    Thread {
        println("Hello!")
    }.start()
}
```



## Kotlin技巧

### 空指针检查

Java中NPE几乎是最Crash的最大原因，而Kotlin内置编译检查来避免NPE，试看下面的这个代码，方法内部的doHomeWork调用安全吗，由于你不知道传递给你的对象是否为null，在调用时就很容易发生NPE。

```java

    public static void doStudy(Study study) {
        study.doHomeWork();
    }
```

你可能会想到，提前做个if，如果不为空时再执行：

```java

    public static void doStudy(Study study) {
        if (study != null) {
            study.doHomeWork();
        }
    }
```

而Kotlin则是非常科学的解决了这个问题，在Kotlin中，上面的代码你可能会写成以下这种形式：

```kotlin
fun main() {
    doWork(null)
}

fun doWork(s: Study) {
    s.doHomeWork()
}
```

但是编译器会在doWork的调用处报错：`Null can not be a value of a non-null type Study`，这是因为Kotlin默认所有的参数和变量都不能为空，所以你传递一个null编译器会报错，但我们后续肯定会遇到值为null的情况，所以Kotlin也给我们提供了解决办法。



如果你想允许某个参数为空的话，只需要在参数类型后面加个`?`即可。

![image-20240805172735374](E:\桌面文件夹\知识\Kotlin\知识.assets\image-20240805172735374.png)

但是此时另外一个错误也来了，这是因为你允许了参数为空，但是方法内又调用了这个参数内部的方法，此时Kotlin就不知道这个参数到底是不是为空：
![image-20240805172758093](E:\桌面文件夹\知识\Kotlin\知识.assets\image-20240805172758093.png)

你可能下意识的想到，再调用之前if判断一下不就好了：

```kotlin
fun doWork(s: Study?) {
    if (s != null) {
        s.doHomeWork()
    }
}
```

这也是办法，但是Kotlin中还有更好的解决办法：使用`?.`和`!!.`，前者代表如果调用的对象不为空则调用，后者则告诉Kotlin编译器忽略这个空指针检查，我自己来确保它不是空的，假设遇到空的时候抛出NPE就行了。`?.`和`!!.`转换成代码就是这样的：

```kotlin
fun doWork(s: Study?) {
    s?.doHomeWork()
}
```

```kotlin
fun doWork(s: Study?) {
    s!!.doHomeWork()
}
```

我们再来学习一个操作符：`?:`，它左右两边各接收一个操作符，如果左边的不为空则直接返回左边的，如果为空则返回右边的，例如一段代码，可以通过它来精简：

```kotlin
fun main() {
    var a = 1
    var b = 2
    var c = if (a != null) {
        a
    }else {
        b
    }
}
```

简化为：

```kotlin
var c =  a ?: b
```

这个操作符有什么用处呢？假设有一个方法需要获取传递过来的字符串的长度，我们就可以通过此操作符进行精简：

```kotlin
fun getStringLength(str : String?): Int {
    if (str != null) return str.length
    return 0
}
```

```kotlin
fun getStringLength(str : String?): Int {
    return str?.length ?: 0
}
```

结合之前学到的函数精简：

```kotlin
fun getStringLength(str : String?) = str?.length ?: 0
```





### 标准函数

Kotlin内置了一些标准函数

#### let函数

let函数它主要用来解决什么问题的呢？还是看doWork这个函数，假设我们需要在study不为空的情况下调用很多study内部的函数，但受限于空指针检查，我们每个调用都需要使用`?.`进行操作，为此我们可以使用let函数。

```kotlin
fun doWork(s: Study?) {
    s?.doHomeWork()
    s?.doReadBook()
}
```

```kotlin
fun doWork(s: Study?) {
    s?.let({
        s.doHomeWork()
        s.doReadBook()
    })
}
```

别忘记了，Lambda的时候我们提到过，如果Lambda表达式是方法的最后一个参数时且只有一个参数时可以省略括号：

```kotlin
fun doWork(s: Study?) {
    s?.let {
        s.doHomeWork()
        s.doReadBook()
    }
}
```

#### with函数

with函数接收两个参数，第一个参数是任意类型的对象，第二个参数是Lambda表达式，with函数会在Lambda表达式中提供第一个参数的上下文，并使用Lambda的最后一行代码作为返回值返回

```kotlin
val result = with(obj) {
 // 这里是obj的上下文
 "value" // with函数的返回值
}
```

那with函数是什么意思呢？举个例子：

比如有一个任务列表，现在我们完成所有任务，并将结果打印出来，正常的写法是这样的：

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "d", "e")
    var res = StringBuffer()
    res.append("开始完成任务...").append("\n")
    for (s in list) {
        res.append("正在完成任务： $s... \n")
    }
    res.append("任务已完成")
    println(res)
}
```

如果使用with函数就可以精简成以下样子：

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "d", "e")
    var res = with(StringBuffer()) {
        append("开始完成任务...\n")
        for (s1 in list) {
            append("正在完成任务： $s1... \n")
        }
        append("任务完成")
    }
    println(res)
}
```

#### run函数

run函数的用法和使用场景其实和 with函数是非常类似的，只是稍微做了一些语法改动而已。首先run函数通常不会直接调用， 而是要在某个对象的基础上调用；其次run函数只接收一个Lambda参数，并且会在Lambda表 达式中提供调用对象的上下文。其他方面和with函数是一样的，包括也会使用Lambda表达式 中的最后一行代码作为返回值返回。示例代码如下：

```kotlin
val result = obj.run {
 // 这里是obj的上下文
 "value" // run函数的返回值
}
```

如果将刚刚例子转到run上就是这样的：

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "d", "e")
    val res = StringBuffer().run {
        append("开始完成任务...\n")
        for (s1 in list) {
            append("正在完成任务： $s1... \n")
        }
        append("任务完成")
    }
    println(res)
}
```

总体来说变化非常小，只是将调用with函数并传入StringBuilder对象改成了调用 StringBuilder对象的run方法，其他都没有任何区别，这两段代码最终的执行结果是完全相 同的。

#### apply函数

apply函数和run函数也是极其类似的，都要在某 个对象上调用，并且只接收一个Lambda参数，也会在Lambda表达式中提供调用对象的上下 文，但是apply函数无法指定返回值，而是会自动返回调用对象本身。示例代码如下：

```kotlin
val result = obj.apply {
 // 这里是obj的上下文
}
// result == obj
```

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "d", "e")
    val res = StringBuffer().apply {
        append("开始完成任务...\n")
        for (s1 in list) {
            append("正在完成任务： $s1... \n")
        }
        append("任务完成")
    }
    println(res)
}

```



### 静态与顶层方法

#### 静态方法

Java中有静态方法的概念，但是在Kotlin中这个静态方法被弱化了，还记得我们使用`object`创建一个单例类吗，创建的单例类我们当时可以使用像静态方法一样的调用方式取调用，但`object`修饰的类会把它的所有方法都变成类似静态方法一样的调用，于是Kotlin提供了companion object修饰符，用于在类中修饰静态方法，那样一个类就可以同时存在静态和非静态方法了。

```kotlin
class Student(name: String, age: Int, val clazz: String): Person(name, age), Study {
    override fun doHomeWork() {
        println("do homework.")
    }
    
    companion object {
        fun sayHello() {
            println("Hello!")
        }
    }
}
```

但是这种方式产生的类似静态方法一样的调用并非真正的静态方法，如果你在Java中使用类似静态方法的调用会发现根本找不到这个方法，`companion object`只是语法上模拟静态方法的调用方式，实际上它们都不是真正的静态方法，因此你如果需要在Java中调用，那么你需要在方法上加上`@JvmStatic`注解

```kotlin
class Student(name: String, age: Int, val clazz: String): Person(name, age), Study {
    override fun doHomeWork() {
        println("do homework.")
    }

    companion object {
        @JvmStatic
        fun sayHello() {
            println("Hello!")
        }
    }
}
```

#### 顶层方法

在kotlin中，顶层方法指的是不用类似静态方法那样的调用方式调用的方法，比如Helper.kt文件中有一个sayHello方法，那么sayHello就是一个顶层方法，你可以在其他类中通过sayHello()直接调用，而不用通过Helper.sayHello()调用。

顶层方法的定义就是单纯一个kt文件，在里面定义的所有fun方法都将做为顶层方法，例如：

```kotlin
package dev.xuanran.kt2

// Helper.kt
fun sayHello() {
    println("Hello!")
}
```

```kotlin
package dev.xuanran.kt2

// Main.kt
fun main() {
    sayHello()
}

```

由于Java中没有顶层方法的概念，所以在Java中如果需要调用顶层方法的话，那么就需要通过Helper.sayHello()的形式调用。

### 延迟初始化

在正常的开发中，类里面肯定存在很多全局变量，而很多全局变量可能只有在类初始化的时候才能去创建实例，但由于Kotlin的变量不可为空的机制，你只能给类似的变量类型后面加上`?`来先手动给它赋值为null(这里的Demo假设student的对象实例需要通过网络获取)。

```kotlin
var student : Student? = null

fun main() {
    // network...
}
```

但是这样会产生一个缺点就是后续你再去调用student内部的方法时，因为你允许这个变量可空，所以每次调用都需要来检查是否为空。

为此，Kotlin提供了延迟初始化功能，使用`lateinit`修饰全局变量，那么就可以避免可空修饰`?`以及后续每次使用前用if判空。

```kotlin
lateinit var student: Student

fun main() {
    // network...
    student.doReadBook()
}

```

但是`lateinit`关键字也不是没有风险，如果你在它之前就调用它，那么还是会产生Exception的。

紧接着Kotlin也给我们提供了一种方式，帮助我们判断某个全局变量是否已初始化：`::变量名.isInitialized`，如果已经初始化过了，我们就可以不用再执行初始化操作了。

```kotlin
lateinit var student: Student

fun main() {
    if (!::student.isInitialized) {
        // network...
    }
    student.doReadBook()
}
```

### lazy懒加载

lazy代码块是Kotlin提供的一种懒加载技术，代码块中的代码一开始并不会执行，只有当修饰的变量首次被调用的时候才会执行，并且将最后一行代码的返回值赋值给被修饰的全局变量，具体语法规则如下：

```kotlin
val p by lazy {
    println("b")
    "c"
}

fun main() {
    println("a")
    println(p)
}
```

输出结果：a  b  c



### 拓展函数

拓展函数是Kotlin中的核心内容，它可以让我们在不修改某个类代码的情况下给其添加新的方法实现。我们都知道Java中如果要去除字符串中的所有空格，我们一般都会使用replaceAll方法去替换所有，然后写成一个Utils类，比如：

```java
public class StringUtils {
    public static String removeSpaces(String str) {
        return str.replaceAll(" ", "");
    }
}
```

我们每次调用都通过工具类的形式进行，而Kotlin可以将你自己定义的方法，添加到String里面当中，直接使用`"".removeSpaces()`的方式去除所有空格，我们就来看一下如何拓展函数的定义语法：

```kotlin
fun ClassName.methodName(param1: Int, param2: Int): Int {
    return 0
}
```

只需要一个ClassName.的函数定义，就可以把这个方法拓展到对应的类当中，还是拿去除空格的例子来说：

```kotlin
fun main() {
    var s = "Hello world!"
    s = s.removeSpaces()
    println(s)
}

fun String.removeSpaces() = this.replace(" ", "")
```

输出结果：Helloworld!

### 运算符重载

Java中的运算符基本只能应用于基本类型的`+-*/`，并不能对类对象进行这一系列的运算符操作，而Kotlin则赋予了我们使类对象也具有这种运算符操作，并且具体操作我们可以自定义。

假设我们存在一个类：`Balance`代表用户的余额，我们需要在其内部新增一个方法，并使用`operator`关键词进行修饰

> 注意，函数名是有要求的，具体的操作符需要的函数名是不一致的，具体操作符与函数名的映射请参加下文。

```kotlin
class Balance(val value: Int) {
    operator fun plus(m : Balance) = Balance(this.value + m.value)
}
```

在这段代码中我们新增了plus函数，它会在Balance对象调用`+`操作时，实际调用的是Balance内部这个plus方法，实例如下：

```kotlin
fun main() {
    val a = Balance(5)
    val b = Balance(1)
    val c = a + b
    println(c.value)
}
```

输出结果：6



#### 运算符表达式和实际调用函数对照表

| 运算符   | 实际调用函数   |
| -------- | -------------- |
| a+b      | a.plus(b)      |
| a - b    | a.minus(b)     |
| a * b    | a.times(b)     |
| a / b    | a.div(b)       |
| a % b    | a.rem(b)       |
| a++      | a.inc()        |
| a--      | a.dec()        |
| +a       | a.unaryPlus()  |
| -a       | a.unaryMinus() |
| !a       | a.not()        |
| a == b   | a.equals(b)    |
| a > b    | a.equals(b)    |
| a < b    | a.equals(b)    |
| a >= b   | a.equals(b)    |
| a <= b   | a.compareTo(b) |
| a..b     | a.rangeTo(b)   |
| a[b]     | a.get(b)       |
| a[b] = c | a.set(b, c)    |
| a in b   | b.contains(a)  |



#### 拓展函数与运算符重载的实践

当我们学习完拓展函数与运算符重载后，我们来举个实践例子，在开发的过程中我们有可能会让字符串的内容重复几遍，比如我想让abc这三个字符在一个字符串中重复n此，你可能会想到写个工具类再写一个for循环，但是学习完拓展函数与运算符重载之后，我们就可以理由Kotlin提供给我们的这两个语法糖来试试：

```kotlin
operator fun String.times(n : Int): String {
    val res = StringBuffer()
    repeat(n) {
        res.append(this)
    }
    return res.toString()
}
```

其中我们使用`operator`来告诉Kotlin此函数为运算符重载，然后使用String.times将这个函数做为String的拓展函数，在方法体内我们又使用repeat来使res重复添加n次。

然后我们就可以使用："abc" * 3，的操作来使字符串重复三遍

```kotlin
fun main() {
    println("abc" * 3)
}
// 输出结果：abcabcabc
```





### 高阶函数

从本节开始，我们将告别基础知识，转向Kotlin高级用法。

#### 高阶函数的定义

高阶函数与Lambda是密不可分的，在Lambda中我们当时使用maxBy函数去求最长的字符串时讲到，maxBy是需要传入一个Lambda参数的，如果你想实现类似这种的，就需要用到高阶函数，那么高阶函数是什么呢？如果一个函数接收另一个函数作为参数，或者返回值的类型是 另一个函数，那么该函数就称为高阶函数。



在Java中我们不能将一个函数做为另外一个函数的参数，而Kotlin却增加了函数类型的概念，如果我们将这种函数类型添加到一个函数的参数声明或者返回值声明当 中，那么这就是一个高阶函数了。



高阶函数语法规则：

```kotlin
(String, Int) -> Unit
```

函数括号内是该函数需要接收什么参数，以及它的返回值是什么，这里的Unit相当于Java中的void关键字，将上述声明添加到方法中，那么此方法就称为高阶函数。	

```kotlin
fun times(func : (String, Int) -> Unit) {
    func("hello", 123)
}
```

高阶函数允许让函数类型的参数来决定函数的执行逻辑。即使是同一个高阶函数，只要传入不同的函数类型参数，那么它的执行逻辑和最终的返回结果就可能是完全不同的



#### 高阶函数实践

知道了高阶函数能够做什么之后，我们来尝试自定义一个我们自己的高阶函数。

定义函数num1AndNum2，参数1为数字1，参数2为数字2，参数3为对应的高阶函数操作，我们在这个方法内返回了这个函数参数的调用，这意味着这个函数的结果是由我们传递的函数参数所决定的。

```kotlin
fun num1AndNum2(n1 : Int, n2 : Int, oper : (Int, Int) -> Int) : Int {
    return oper(n1, n2)
}
```

然后在`HigherOrderFunction.kt`文件中添加以下内容，其中plus主要返回n1和n2的相加结果，minus返回n1-n2的结果：

```kotlin
fun plus(n1 : Int, n2 : Int): Int {
    return n1 + n2
}

fun minus(n1 : Int, n2 : Int): Int {
    return n1 - n2
}
```

接着，我们将参数和具体的函数参数传递给num1AndNum2这个函数

```kotlin
fun main() {
    println(num1AndNum2(1, 2, ::plus))
}
```

输出结果：3



但是你会想，这样也太麻烦了吧，我为什么不直接去调用`HigherOrderFunction.kt`，但是函数参数不仅可以使用函数引用的方式(上文提到的方式)，还可以使用Lambda、匿名函数、成员引用等，比如我们可以不用在`HigherOrderFunction.kt`中定义函数，而是在调用num1AndNum2时，使用Lambda效果也是一样的：

```kotlin
println(num1AndNum2(1, 2) { n1, n2 -> n1 + n2 })
```



#### 高阶函数与apply

使用高阶函数还可以实现apply提供一个对象上下文的功能，新增如下方法：

```kotlin
fun StringBuffer.build(block: StringBuffer.() -> Unit): StringBuffer {
    block()
    return this
}
```

可以看到我们这里使用到了拓展函数，但是奇怪的点是函数参数并不是我们之前提到的高阶函数定义的标准格式，实际情况是，我们现在使用的才是高阶函数的标准形式，在函数参数括号前使用`ClassName.`来告诉Kotlin，我的这个高阶函数是定义在哪个Class里面的。

```kotlin
fun main() {
    val list = listOf("a", "b", "c")
    val res = StringBuffer().build {
        append("开始完成任务... \n")
        for (s in list) {
            append("正在完成: $s \n")
        }
    }
    println(res)
}
```

然后我们就可以不用在使用apply来提供一个对象的上下文了。



### 内联函数

Kotlin和Java最后都是要编译成Java字节码的，但是高阶函数你可能觉得十分神奇，为什么在Java中没有呢？实际上高阶函数在Java中可以通过函数式接口的方式实现：

```java
public static int num1AndNum2(int num1, int num2, Function operation) {
 int result = (int) operation.invoke(num1, num2);
 return result;
}
public static void main() {
 int num1 = 100;
 int num2 = 80;
 int result = num1AndNum2(num1, num2, new Function() {
 @Override
 public Integer invoke(Integer n1, Integer n2) {
 return n1 + n2;
 }
 });
}
```

这个并不是十分严谨的Kotlin转换Java的代码，我们可以看到在Kotlin中我们使用高阶函数时，实际上是Kotlin通过函数式接口创建了一个匿名内部类，并通过invoke的调用才完成的，但是如果频繁的创建匿名内部类一个不好的习惯，因为它会带来性能开销，为此，Kotlin提供了内联函数。



内联函数非常简单，只需要在创建高阶函数时使用`inline`修饰即可。

```kotlin
inline fun num1AndNum2(n1 : Int, n2 : Int, oper : (Int, Int) -> Int) : Int {
    return oper(n1, n2)
}
```

使用内联函数后，Kotlin编译器将不会给其编译成匿名内部类的形式，而是使用会将Lambda表达式中的代码替换到函数类型参数调用的地方。







## 泛型与委托



### 泛型

Kotlin的泛型与Java十分类似，也是使用<T>这种的语法结构：

```kotlin
class Fruit<T> {
    fun eat(f : T): Unit {
        println("eat...")
    }

    fun <T> buy(p : T): T {
        return p
    }
}
```



#### 泛型限制

和Java一样，Kotlin也允许泛型是某个类的子类

```kotlin
fun <T : Number> other(p : T): T {
        return p
}
```





#### 泛型实化

Java的泛型是使用泛型擦除机制来实现的，所以只存在于编译时期，所以所有基于JVM的语言他们的泛型都是通过类型擦除机制实现的，Kotlin也不例外，这种机制使得我们不可能使用`a is T`或者`T::class.java`这些的写法了，因为T在运行时类型已经被擦除了。

然而Kotlin剔红了一个内联函数的概念，内联函数在编译时会自动被替换它被调用的地方，这样也就不存在什么泛型擦除的问题了，因为代码在编译的时候会直接使用实际的类型来替代内联函数中的泛型声明。

这意味着Kotlin是可以将内联函数中的泛型进行实化的，那怎样实现呢？首先函数必须是内联函数且声明泛型的地方需要使用`reified`进行修饰，举个例子：

```kotlin
fun main() {
    var r = getGenericType<String>()
    var m = getGenericType<Int>()
    println("r is $r")
    println("m is $m")
}

inline fun <reified T> getGenericType() = T::class.java
```

输出结果：r is class java.lang.String
m is class java.lang.Integer

而Java是不可能实现上述功能的，因为在编译后，泛型这个约束对于JVM已经不存在了。



#### 泛型协变

泛型的协变与逆变功能就基本用不到，但是还是讲一下，避免在看别人代码的时候看不懂是什么意思。

首先我们需要讲一个概念，在泛型类或者泛型方法中，参数列表是接收数据的地方，因此可以称它的位置是in位置，而返回类型的地方是输出数据的地方，因此可以称它为out。

接下来我们定义三个类：

```kotlin
open class Person(val name: String, val age: Int)
class Student(name: String, age: Int): Person(name, age)
class Teacher(name: String, age: Int): Person(name, age)
```

接下来问你一个问题，假设在Java中一个方法接收Person类型的参数，而你传递一个Student可以吗，当然可以吧。

但是，如果方法接收的参数是一个List<Person>你传递一个List<Student>这样可以吗，这样是不行的，不行你去试一下，不行的原因主要是Java为了防止类型转换的安全隐患，我们举个例子，假设有个Data的泛型类：

```kotlin
class Data<T> {
    var value: T? = null

    fun set(v: T?) {
        this.value = v
    }

    fun get(): T? {
        return this.value
    }
}
```

```kotlin

fun main() {
    var s = Student("XuanRan", 18)
    var d = Data<Student>()
    d.set(s)
    handle(d) // 此行报错
    println(d.get())
}

fun handle(data: Data<Person>) {
    data.set(Teacher("Tom", 17))
}
```

我们假设报错的那一行能够正常编译通过，按照程序逻辑，如果我们上面提到的问题允许的话，那么经过handle处理后的Data将不再是原来的Student，所以为了杜绝这种情况，Java直接禁用掉了这种方式，换句话讲，即使Student是Person的子类，但是Data<Student>并不是Data<Person>的子类。



不过，我们一起回顾一下代码，出现这种问题的主要原因是因为我们在handle方法内部，通过data给设置了Teacher对象，如果Data在泛型T上是只读的话，那肯定没有类型转换异常了，所以我们只要确保泛型类在其泛型数据上是只读的话那么它是没有类型转换安全隐患的，换句话说我们在Data类里面，只要保留get方法，不要暴露set方法供外部调用，只在构造方法中进行参数设置即可，要实现这一点，只要让这个泛型类的所有方法(构造除外)都不能去接收T类型的参数，换句话说，T只能出现在out位置，而不是in位置，如下代码所示：

```kotlin
class Data<out T>(val value: T) {

    fun get(): T? {
        return this.value
    }
}
```

我们在T之前使用out进行修饰，此时类中所有含参数T的方法都会报错，并且我们在这个类中添加常量value，此时，我们再或过头看之前的代码，会发现handle调用不报错了，而handle方法的实现中，调用Data内部的set方法报错，这很正常，因为我们删掉了set。

```kotlin
fun main() {
    var s = Student("XuanRan", 18)
    var d = Data(s)
    handle(d)
    println(d.get())
}

fun handle(data: Data<Person>) {
    // todo
}
```

这样我们就实现了泛型的协变。

除此之外，Kotlin已经给很多内置的API加上了协变的声明，其中就包括了各种集合类与接口，还记得我们之前提到的使用listOf只能创建只读的集合吗，我们现在就来看一下List的精简源码

```kotlin
public interface List<out E> : Collection<E> {
 override val size: Int
 override fun isEmpty(): Boolean
 override fun contains(element: @UnsafeVariance E): Boolean
 override fun iterator(): Iterator<E>
 public operator fun get(index: Int): E
}
```

List在泛型E的前面加上了out关键字，说明List在泛型E上是协变的。不过这里还有一点需要说 明，原则上在声明了协变之后，泛型E就只能出现在out位置上，可是你会发现，在 contains()方法中，泛型E仍然出现在了in位置上。

这么写本身是不合法的，因为在in位置上出现了泛型E就意味着会有类型转换的安全隐患。但是 contains()方法的目的非常明确，它只是为了判断当前集合中是否包含参数中传入的这个元 素，而并不会修改当前集合中的内容，因此这种操作实质上又是安全的。那么为了让编译器能够理解我们的这种操作是安全的，这里在泛型E的前面又加上了一个@UnsafeVariance注解， 这样编译器就会允许泛型E出现在in位置上了。



#### 泛型逆变

我们定义一个Transformer对象，并接收一个Person的泛型，此外要求接口实现类必须重写自己的trans方法，然后我们在main中实现了这个接口，这个接口trans的返回值将会返回Person的名字和姓名，并且定义了一个方法handle，用于输出一个学生的姓名，但是handle在调用的时候会报错语法错误，因为方法要求接收的是Transformer<Student>的泛型，而传递过来的却是Transformer<Person>的泛型，换句话说，Transformer<Person>并不是Transformer<Student>的子类型。

```kotlin
interface Transformer<T> {
    fun trans(t: T): String
}

fun main() {
    // 实现一个Transformer接口
    val t = object : Transformer<Person> {
        // 定义我们自己的trans方法
        override fun trans(t: Person): String {
            // 该方法会返回Person类的名称和年龄
            return "${t.name} ${t.age}"
        }
    }
    handle(t) // 此处语法错误
}

/**
 * 该函数接收一个Transformer的对象，要求泛型为Student
 */
fun handle(s: Transformer<Student>) {
    var student = Student("XuanRan", 12)
    println(s.trans(student))
}
```

要解决这个问题，我们就可以使用泛型的逆变，也就是in关键字，修改Transformer代码，如下所示：

```kotlin
interface Transformer<in T> {
    fun trans(t: T): String
}
```

然后代码就能正常编译输出了，这就是泛型的逆变。



### 委托

委托是一种设计模式，它的基本理念是：操作对象自己不会去处理某段逻辑，而是会把工作委 托给另外一个辅助对象去处理。这个概念对于Java程序员来讲可能相对比较陌生，因为Java对 于委托并没有语言层级的实现，而像C#等语言就对委托进行了原生的支持。

Kotlin中也是支持委托功能的，并且将委托功能分为了两种：类委托和委托属性。

#### 类委托

有时我们需要在Set集合上定制我们自己的方法，比如增加一个hello方法，我们可能使用Java的话可能会下意识的去实现Set接口，然后在构造方法中传入一个HashSet，实际的增删改查还是使用HashSet来进行，我们只是实现了Set接口，并增加了一个hello方法。

```kotlin
class MySet<T>(val set: HashSet<T>) : Set<T> {
    override val size: Int
        get() = set.size

    override fun isEmpty(): Boolean {
        return set.isEmpty()
    }

    override fun iterator(): Iterator<T> {
        return set.iterator()
    }

    override fun containsAll(elements: Collection<T>): Boolean {
        return set.containsAll(elements)
    }

    override fun contains(element: T): Boolean {
        return set.contains(element)
    }
    
    fun hello() {
        println("hello!")
    }
}
```

可以看到，我们不得不重写Set接口必须实现的抽象方法，如果重写的少还好，但是如果需要重写的多呢，Kotlin在面对这种的情况给我们提供了类委托，类委托主要使用`by 受委托对象`即可

```kotlin
class MySet<T>(val set: HashSet<T>) : Set<T> by set{
    fun hello() {
        println("hello!")
    }
}
```

#### 委托属性

委托属性的核心思想是将 一个属性（字段）的具体实现委托给另一个类去完成。

我们来看一下委托属性的语法结构：

```kotlin
class MyClass {
 var p by Delegate()
}
```

这里使用by关键字连接了左边的p属性和右边的Delegate实例，这是什么意思呢？ 这种写法就代表着将p属性的具体实现委托给了Delegate类去完成。当调用p属性的时候会自 动调用Delegate类的getValue()方法，当给p属性赋值的时候会自动调用Delegate类的 setValue()方法。

因此，我们还得对Delegate类进行具体的实现才行.

```kotlin
class Delegate {
    var propValue: Any? = null
    operator fun getValue(clazz: MyClass, prop : KProperty<*>): Any? {
        return propValue
    }

    operator fun setValue(clazz: MyClass, prop: KProperty<*>, value: Any?) {
        propValue = value
    }
}
```

这是一种标准的代码实现模板，在Delegate类中我们必须实现getValue()和setValue()这 两个方法，并且都要使用operator关键字进行声明。

getValue()方法要接收两个参数：第一个参数用于声明该Delegate类的委托功能可以在什么 类中使用，这里写成MyClass表示仅可在MyClass类中使用；第二个参数KProperty<*>是 Kotlin中的一个属性操作类，可用于获取各种属性相关的值，在当前场景下用不着，但是必须在 方法参数上进行声明。另外，<?>这种泛型的写法表示你不知道或者不关心泛型的具体类型，只是为了通过语法编译而已，有点类似于Java中的写法。

至于返回值可以声明成任何类型，根据具体的实现逻辑去写就行了，上述代码只是一种示例写法。 setValue()方法也是相似的，只不过它要接收3个参数。前两个参数和getValue()方法是相 同的，最后一个参数表示具体要赋值给委托属性的值，这个参数的类型必须和getValue()方 法返回值的类型保持一致。 整个委托属性的工作流程就是这样实现的，现在当我们给MyClass的p属性赋值时，就会调用 Delegate类的setValue()方法，当获取MyClass中p属性的值时，就会调用Delegate类的getValue()方法





#### 委托实践：实现lazy懒加载

学完委托有没有发现，lazy懒加载，实际只是一个高阶函数+Lambda+委托属性的实现，通过高阶函数我们可以将一个函数传递给另外一个函数，Lambda允许我们将参数置于`{}`内，委托属性允许当我们调用属性时去才去执行其的getValue方法，这不就是lazy吗，我们完全可以利用之前学到的知识来定义我们自己的懒加载实现。



定义类：Later

```kotlin
class Later<T>(val block : () -> T) {
    var value : Any? = null
    operator fun getValue(any : Any?, prop: KProperty<*>): T {
        if (this.value == null) {
            this.value = block()
        }
        return this.value as T
    }
}

fun <T> later(block: () -> T) = Later(block)
```

除了定义Later之外，我们还定义了一个顶层方法以帮助我们更加便捷的使用，此时自定义的Lazy便完成了，我们可以写个Demo试一下。

```kotlin
val p by later {
    println("b")
    "c"
}

fun main() {
    println("a")
    println(p)
}
```

输出结果：a
b
c

可以看到与lazy的效果是一致的，从这里你应该可以看出Kotlin并没有想象中的那么神秘。







## infix函数

### infix定义

在集合与Lambda章节中我们曾经见到过这样的语法，我们使用to来创建Map的Key与Value的映射：

```kotlin
var m = mapOf("a" to 1, "b" to 2)
```

但是，当时我们没提到，这里的to其实并不是Kotlin的关键字，而是一个infix函数，至于什么是infix函数呢，我举个例子你就懂了。



String中有个startsWith方法是判断字符串是否已某个特定字符开头的，我们来给它包装一下，并定义成拓展方法

```kotlin
infix fun String.startWith2(str: String): Boolean {
    return this.startsWith(str)
}
```

与标准的拓展方法不同的是，我们这里使用了infix修饰这个函数，然后我们就可以使用to的那种语法来使用我们定义的这个拓展函数了。

```kotlin
fun main() {
    if ("abc" startWith2 "a") {
        println("yes")
    }
}

infix fun String.startWith2(str: String): Boolean {
    return this.startsWith(str)
}
```

所以，infix函数的作用就是可以允许我们省略掉调用时的`.`和括号，但是由于infix的特性，所以它无法被定义成顶层方法，并且它的参数只能有一个，只有同时满足这两个条件，infix才能使用。



### infix实践：判断集合中是否包含指定元素

学习完infix，自己手动试一下，定义一个infix，判断list内是否包含指定的元素，建议自己尝试之后再看后面的答案。

```kotlin
fun main() {
    val list = listOf("a", "b", "c")
}
```

由于List是继承子Collection的，我们直接给Collection添加拓展方法，这里用到了泛型的概念，忘记的同学记得回去复习：

```kotlin
infix fun <T> Collection<T>.has(element : T) = contains(element)
```

```kotlin
fun main() {
    val list = listOf("a", "b", "c")
    if (list has "a") {
        println("yes")
    }
}

infix fun <T> Collection<T>.has(element : T) = contains(element)
```



### infix实践：实现to的效果

如果我们在使用to的时候点进去，就可以看到to的源码是这样的：

```kotlin
public infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)
```

可以看到，这里使用定义泛型函数的方式将to()函数定义到了A类型下，并且接收一个B类型的 参数。因此A和B可以是两种不同类型的泛型，也就使得我们可以构建出字符串to整型这样的键 值对。

再来看to()函数的具体实现，非常简单，就是创建并返回了一个Pair对象。也就是说，A to B这样的语法结构实际上得到的是一个包含A、B数据的Pair对象，而mapOf()函数实际上接收的正是一个Pair类型的可变参数列表，这样我们就将这种神奇的语法结构完全解密了，然后我们来手动实现这个to的语法结构。

```kotlin
infix fun <A,B> A.with(that: B): Pair<A, B> = Pair(this, that)
```

```kotlin
val m2 = mapOf("a" with 1, "b" with 2)
```





## 协程

协程和Java最新出的虚拟线程十分类似，可以简单地将它理解成一种轻量级的线程。 要知道，我们之前所学习的线程是非常重量级的，它需要依靠操作系统的调度才能实现不同线程之间的切换。而使用协程却可以仅在编程语言的层面就能实现不同协程之间的切换，从而大大提升了并发编程的运行效率。

协程并没有在Kotlin标准库当中，需要额外引入依赖：

```xml
        <!-- https://mvnrepository.com/artifact/org.jetbrains.kotlinx/kotlinx-coroutines-core -->
        <dependency>
            <groupId>org.jetbrains.kotlinx</groupId>
            <artifactId>kotlinx-coroutines-core</artifactId>
            <version>1.6.4</version>
            <type>pom</type>
        </dependency>
```

开启一个协程最简单的办法就是使用GlobalScope.launch函数创建一个协程的作用域。

```kotlin
fun main() {
    GlobalScope.launch {
        println("Hello!")
    }
    Thread.sleep(1000)
}
```

但是此处我还使用sleep将主线程休眠1秒，因为协程相当于一个线程，有可能内容还没输出呢，主进程就结束完成了，除此之外，我们还可以在协程中使用delay函数来挂起协程，它与sleep不同的是它不会影响到其他协程的运行，而sleep会阻塞当前线程造成当前线程下的所有协程都被阻塞。
