
 ### Learning Objectives
	- To **revise the concepts of class and object**.
	- To realize that **a program that has been written without objects** can **also be written using objects**.
	- To realize that the **use of objects** can **make a program more understandable**.







 ## object-oriented programming 为什么需要OOP，class和object

 假设我们现在需要实现一个时钟时针、分针、秒针的运行方式，The time is always printed in the form `hours: minutes: seconds`，由两位数构成（where two digits are used to represent the hour (e.g., 01 or 12) as well as the minutes and seconds.）

 1. 没有class和object的**最原始写法**能运行，但很混乱
	- ps：这里代码一直在持续循环加时间，但是**不是现实世界中真正“一秒走一次”的钟**，电脑会快速循环，可能一秒钟就打印几万行。
```java
int hours = 0;
int minutes = 0;
int seconds = 0;

while (true) {
    // 1. Printing the time
    if (hours < 10) {
        System.out.print("0");
    }
    System.out.print(hours);

    System.out.print(":");

    if (minutes < 10) {
        System.out.print("0");
    }
    System.out.print(minutes);

    System.out.print(":");

    if (seconds < 10) {
        System.out.print("0");
    }
    System.out.print(seconds);
    System.out.println();

    // 2. The second hand's progress
    seconds = seconds + 1;

    // 3. The other hand's progress when necessary
    if (seconds > 59) {
        minutes = minutes + 1;
        seconds = 0;

        if (minutes > 59) {
            hours = hours + 1;
            minutes = 0;

            if (hours > 23) {
                hours = 0;
            }
        }
    }
}
```


 2. 因为**本质上时针、分针和秒针都是一个东西**，**都有当前值`value`和上限`limit`**，所以**我们可以创建一个 `ClockHand` class**。
```java
ClockHand
│
├── value       当前数值
├── limit       最大值
│
├── advance()   前进一步
├── value()     查看当前值
└── toString()  怎么显示

// 例如
ClockHand seconds = new ClockHand(60);
意思是 > 创建一个“上限是 60 的钟表指针”。
```
 代码如下：
```java
public class ClockHand {
    private int value;
    private int limit;

    public ClockHand(int limit) {
        this.limit = limit;
        this.value = 0;
    }

    public void advance() {
        this.value = this.value + 1;

        if (this.value >= this.limit) {
            this.value = 0;
        }
    }

    public int value() {
        return this.value;
    }

    public String toString() {
        if (this.value < 10) {
            return "0" + this.value;
        }

        return "" + this.value;
    }
}
```
 调用这个`ClockHand`类创建对象
```java
ClockHand hours = new ClockHand(24);
ClockHand minutes = new ClockHand(60);
ClockHand seconds = new ClockHand(60);

while (true) {
    // 1. Printing the time
    System.out.println(hours + ":" + minutes + ":" + seconds);

    // 2. Advancing the second hand
    seconds.advance();

    // 3. Advancing the other hands when required 这里的意思是，如果秒+1导致超过limit秒归零了，那么要给分钟+1
    if (seconds.value() == 0) {
        minutes.advance();

        if (minutes.value() == 0) { // 同理，如果分钟+1导致超过limit秒归零了，那么要给小时+1
            hours.advance();
        }
    }
}
```


 3. **在`ClockHand`类这个基础上**，**其实我们还可以再写一个`Clock`类把所有外部调用ClockHand类的代码都封装进去**。
```java
public class Clock {
    private ClockHand hours;
    private ClockHand minutes;
    private ClockHand seconds;

    public Clock() {
        this.hours = new ClockHand(24);
        this.minutes = new ClockHand(60);
        this.seconds = new ClockHand(60);
    }

    public void advance() {
        this.seconds.advance();

        if (this.seconds.value() == 0) {
            this.minutes.advance();

            if (this.minutes.value() == 0) {
                this.hours.advance();
            }
        }
    }

    public String toString() {
        return hours + ":" + minutes + ":" + seconds;
    }
}
```
 这样我们的外部调用可以变的非常简单
```java
Clock clock = new Clock();

while (true) {
    System.out.println(clock);
    clock.advance();
}
```
This is precisely the **great idea behind ​​object-oriented programming: a program is built from small and distinct objects that work together**


 ps：上面的3的Clock中的advance method还调用了ClockHand的advance method
	- java区分的方法主要是看**对哪个对象调用 `advance()`**。
	- 因为**我们定义的是`private ClockHand seconds;`**，所以this.seconds.advance();就会调用**ClockHand 类里的 `advance()`**。
		- 这里的`this`可以省略。
```java
public void advance() {
        this.seconds.advance();
```











 ## Object 对象

 ==An **Object** refers to **an independent entity** that contains both **data (instance variables)** and **behavior (methods)**.==
```java
Object
├── data        → instance variables / fields
└── behavior    → methods
```

 a **class** contains **the blueprint needed to create objects**, and also **defines the objects' variables and methods**. （class就相当于蓝图，定义了objects的variables和methods）
 一个 class 不需要描述现实世界对象的全部信息。When building an application that deals with people, the **functionality and features related to a person are gathered based on the application's use case**.

 ==**The state of an object** is **the value of its internal variables at any given point in time**==. **一个 object 的 state（状态）**，就是**在某一个具体时刻，它内部所有 instance variables 当前分别是什么值**。

 例子：a Person object that keeps track of name, age, weight, and height, and provides the ability to calculate body mass index and maximum heart rate.
```java
public class Person {
    private String name;
    private int age;
    private double weight;
    private double height;

    public Person(String name, int age, double weight, double height) {
        this.name = name;
        this.age = age;
        this.weight = weight;
        this.height = height;
    }

    public double bodyMassIndex() {
        return this.weight / (this.height * this.height);
    }

    public double maximumHeartRate() {
        return 206.3 - (0.711 * this.age);
    }

    public String toString() {
        return this.name + ", BMI: " + this.bodyMassIndex()
            + ", maximum heart rate: " + this.maximumHeartRate();
    }
}
```
 调用上面的`Person` class，计算body mass index和max heart rate：
```java
Scanner reader = new Scanner(System.in);
System.out.println("What's your name?");
String name = reader.nextLine();
System.out.println("What's your age?");
int age = Integer.valueOf(reader.nextLine());
System.out.println("What's your weight?");
double weight = Double.valueOf(reader.nextLine());
System.out.println("What's your height?");
double height = Double.valueOf(reader.nextLine());

Person person = new Person(name, age, weight, height);
System.out.println(person);
```
![[Screenshot 2026-09-09 at 13.14.49.png|804]]










 ## Class

 一个Class决定了它能创建出什么样的object。它由3部分构成
	- **instance variables**
	- **constructor**
	- **methods**
```java
Class
├── instance variables   对象“有什么数据”
├── constructor          对象“出生时怎么初始化”
└── methods              对象“能做什么”
```
 比如下面的`Rectangle` Class结构
```java
Rectangle
├── width
├── height
│
├── Rectangle(width, height)
│
├── widen()
├── narrow()
├── surfaceArea()
└── toString()
```
 具体代码
	- `void`不返回任何值
	- `toString` methods会返回print(对象名)时会打印出来的值
```java
// class
public class Rectangle {

    // instance variables
    private int width;
    private int height;

    // constructor
    public Rectangle(int width, int height) {
        this.width = width;
        this.height = height;
    }

    // methods
    public void widen() {
        this.width = this.width + 1;
    }

    public void narrow() {
        if (this.width > 0) {
            this.width = this.width - 1;
        }
    }

    public int surfaceArea() {
        return this.width * this.height;
    }

    public String toString() {
        return "(" + this.width + ", " + this.height + ")";
    }
}
```
 **Objects** are **created from the class** ==through constructors== by **using the `new` command**. 下面创建两个Rectangles然后打印他们的相关信息：
```java
Rectangle first = new Rectangle(40, 80);
Rectangle rectangle = new Rectangle(10, 10);
System.out.println(first);
System.out.println(rectangle);

first.narrow();
System.out.println(first);
System.out.println(first.surfaceArea());
```
![[Screenshot 2026-09-09 at 13.23.15.png|778]]

