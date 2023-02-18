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
------



## 2.==和equals比较

**回答**:`==`对比的是`栈`中的值，是基本数据类型是变量值。而`引用类型`是`堆中内存对象`的地址

重写String的equals方法（）。

```java
	// Object
    public boolean equals(Object obj) {
        return (this == obj);
    }
    // String
    public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

上述代码可以看出，String类中被复写的equals()方法其实是比较两个字符串的内容。

```java
public class StringDemo {
    public static void main(String args[]) {
        String str1 = "Hello";
        String str2 = new String("Hello");
        String str3 = str2; // 引用传递 
        System.out.println(str1 == str2); // false 
        System.out.println(str1 == str3); // false 
        System.out.println(str2 == str3); // true 
        System.out.println(str1.equals(str2)); // true  两个字符串的内容相同
        System.out.println(str1.equals(str3)); // true 
        System.out.println(str2.equals(str3)); // true 
    }
}
```

**总结**：==比较栈中的引用值或者是值，equals()比较堆中两个字符串内容。Object中equals方法默认方法是==，但是一般都会重写，比如说string重写了object中equals的方法，比较的是两个字符数组的内容。例如上面代码。

------



## 3.hashCode与equals

**hashCode**:hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。

`哈希码的作用`是：对象在堆中的`索引`

确定该对象在`哈希表`（对象在堆中存放位置）中的索引（hashCode）位置。hashCode() 定义在JDK的Object.java中，Java中的任何类都包含有hashCode() 函数。散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

**equals**:

java提供的用来对比两个对象什么时候是相等的，什么时候是不等的。用什么规则来定义，就是用equals方法，可以重写equals方法来定义两个对象的对比规则，按什么规则来对比来判断两个对象是否相等。



为什么要有hashCode： 

**以“HashSet如何检查重复”为例子来说明为什么要有hashCode：**

对象加入HashSet时，HashSet会先计算对象的hashcode值来判断对象加入的位置，看该位置是否有值，如果没有、HashSet会假设对象没有重复出现，则把对象放入当前位置。但是如果发现有值，这时会调用equals（）方法来检查两个对象是否真的相同。如果两者相同，HashSet就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样就大大减少了equals的次数，相应就大大提高了执行速度。

1. 如果两个对象相等，则hashcode一定也是相同的

2. 两个对象相等,对两个对象分别调用equals方法都返回true

3. 两个对象有相同的hashcode值，它们也不一定是相等的

4. 因此，equals方法被覆盖过，则hashCode方法也必须被覆盖

5. hashCode()的默认行为是对堆上的对象产生独特值。如果没有重写hashCode()，则该class的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

```
 扩展：hashCode()是怎么算出来值的？
- Java中的hashCode方法是一个内置的方法，每个对象都继承了Object类的hashCode方法，因此可以在任何对象上调用hashCode方法，如果对象没有重写hashCode方法，则默认情况下会返回对象的`内存地址`的哈希码。然而，大多数类都会重写hashCode方法，以便更准确地计算哈希码。
对于自定义类，如果你想要在hashCode方法中使用该类的属性来计算哈希码，就需要确保该类中的每个属性都正确地实现了hashCode方法。一般来说，如果属性是一个基本类型（比如int、double等），则可以直接使用它的hashCode方法；如果属性是一个自定义类型，则需要`递归`地调用该类型的hashCode方法。这样，就能够准确地计算出对象的哈希码。
```

------


