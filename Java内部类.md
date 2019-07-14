---
title: Java内部类
date: 2019-07-07 08:38:31
tags: Java
---

## 关于内部类

顾名思义，内部类就是定义在一个类中的类。内部类除了可以像一般的类一样，继承超类、实现接口、访问自己的数据等等，还可以访问外部类的数据（包括私有数据）。

我们可以将某些内部类看作外部类的一个属性，一样可以使用访问修饰符来修饰且完全符合相应的规则。



## 使用内部类访问对象状态

```java
public class OuterClass {
	private int a = 23;
	
	public class InnerClass {
		public void printA() {
			System.out.println(a);
		}
	}
}
```

内部类对象中有一个隐式引用指向外部类对象，就像上面的代码中，内部类对象中访问的**变量a**明显就是**外部类对象的属性**:

![](./inner_class.png)

如果要在代码中显示地表达这种关系，则需要使用语法 **`外部类名.this`** ，它代表**调用当前方法的对象所对应的外部类对象**。

```java
public class OuterClass {
	private int a = 23;
	
	public class InnerClass {
		public void printA() {
			System.out.println(OuterClass.this.a);
		}
	}
}
```



## 局部内部类

局部内部类是定义于代码块中的内部类，即其作用域就被限定在该代码块内，且不能使用访问修饰符来修饰。

```java
public class OuterClass {
	public void printInnerA() {
		class InnerClass {
			private String a = "Hello~";
			
			public void printA() {
				System.out.println(a);
			}
		}
		
		InnerClass innerClass = new InnerClass();
		innerClass.printA();
	}
	
	public static void main(String[] args) {
		OuterClass outerClass = new OuterClass();
		outerClass.printInnerA();
	}
}
```

对于一些临时且简单的类，则可以使用局部内部类来定义。另外，局部内部类除了可以访问外部类以外，还可以访问代码块中的局部变量。



## 匿名内部类

当局部内部类要继承类或实现接口时，我们可以这样写

```java
public class OuterClass {
	public void startThread() {
		Runnable inner = new Runnable() {
			public void run() {
				System.out.println("running...");
			}
		};
		
		Thread thread = new Thread(inner);
		
		thread.start();
	}
	
	public static void main(String[] args) {
		OuterClass outerClass = new OuterClass();
		outerClass.startThread();
	}
}
```

还可以写得再简单一些：

```java
public void startThread() {

    Thread thread = new Thread(new Runnable() {
        public void run() {
            System.out.println("running...");
        }
    });

    thread.start();
}
```

甚至更简单一点：

```java
public void startThread() {

    new Thread(new Runnable() {
        public void run() {
            System.out.println("running...");
        }
    }).start();
}
```



## 静态内部类

```java
public class ArrayAlg {
	public static class Pair {
		private double first;
		private double second;
		
		public Pair(double f, double s) {
			first = f;
			second = s;
		}
		
		public double getFirst() { return first; }
		public double getSecond() { return second; }
	}
	
	public static Pair minmax(double[] values) {
		double min = Double.POSITIVE_INFINITY;
		double max = Double.NEGATIVE_INFINITY;
		for (double v : values) {
			if (min > v) min = v;
			if (max < v) max = v;
		}
		return new Pair(min, max);
	}
	
	public static void main(String[] args) {
	    ArrayAlg.Pair pair = ArrayAlg.minmax(new double[] {1.32,32,43,43.21,-3.2});
	    System.out.println(pair.getFirst()+" "+pair.getSecond());
	}
}
```



## 成员内部类

以实现一个链表为例子，链表的由结点构成，每个结点除了保存元素以外，还要保存指向下一个结点的地址。为了方便我们实现，我们可以文结点定义一个内部类。

```java

public class Link<Item> implements Iterable<Item> {
    private int size;
    private Node<Item> first;

    private class Node<Item> {
        private Item item;
        private Node next;
    }

    
   // ......
       
}
```

