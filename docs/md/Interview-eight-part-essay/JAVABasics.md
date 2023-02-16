# JAVA基础

## 1.什么是面向对象？

**回答：**面向对象与面向过程，是两种不同的处理问题的角度。面向过程更注重一件事情的过程与步骤，面向对象更注重一件事情有哪些参与者（对象）、及各自需要做什么。

比如拿`我`来面试举例子：

`面向过程`：我到公司->找面试官->到会议室面试我

`面向对象`：会把过程拆分成两个或者多个对象，比如上面说面试的例子，可以拆分成我和面试官两个对象。

我负责到公司、然后准备面试这两个属性，面试官负责面试我一个属性。

面向对象更易于扩展、复用和维护。

面向对象三大特性：封装、继承、多态。

* 封装：内部细节对外部调用透明，外部调用无需修改或者关心内部实现。

```举例：1.javaBean属性私有，提供get()set()方法对外访问,因为属性的赋值或者获取逻辑只能由javaBean本身决定，而不能由外部胡乱修改。```

```2.orm框架：操作数据库，我们不需要关心连接如何建立、sql如何实现、只需要引入mybatis调用方法即可。```

* 继承：继承基类的方法，并作出自己的改变或扩展。子类共性的方法或者属性直接使用父类的，而不需要自己再定义，也可以扩展自己个性化的。

* 多态：继承方法重写，父类引用指向子类对象。

  eg:有两个类继承了同一个父类

  ​	    父类类型 变量名1 = new 子类对象1 ; 变量名1.方法名();

  ​		父类类型 变量名2 = new 子类对象2 ; 变量名2.方法名();


***代码演示***

```java

public class Animal {
    public void makeSound() {
        System.out.println("Animal is making a sound");
    }
}

public class Cat extends Animal {
    public void makeSound() {
        System.out.println("Meow");
    }
}

public class Dog extends Animal {
    public void makeSound() {
        System.out.println("Woof");
    }
}

public class Main {
    public static void main(String[] args) {
        Animal myCat = new Cat(); 
        Animal myDog = new Dog();
        myCat.makeSound();
        myDog.makeSound();
    }
}
```

在这个例子中，`Animal`是一个父类，而`Cat`和`Dog`是`Animal`的两个子类。

`Animal`类中定义了一个makeSound（）方法，而`Cat`和`Dog`类都继承了`Animal`类，并且重写了makeSound（）方法。

在Main类中，我们定义了一个myCat对象和一个myDog对象，分别使用`Cat`和`Dog`类来实例化。然后我们分别调用它们的makeSound（）方法。这里要注意，我们使用的是`Animal`类型的引用，而不是具体的`Cat`或`Dog`类型。这就是Java中的多态，因为我们不需要关心对象的具体类型，只需要知道它是`Animal`类型就可以调用它的makeSound（）方法。在运行时，Java会根据对象的具体类型调用正确的makeSound（）方法。


```java
//输出结果为
Meow
Woof
```



