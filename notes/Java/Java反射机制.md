## 反射机制

Java反射机制是指在程序的**运行过程中**，获取一个类的信息或是操纵该类，通过反射可以于运行时加载、探知、使用**编译期间完全未知**的类。虽然Java被归为类似于C一样的静态语言，但通过反射机制又让Java拥有了动态的能力。



## Class类

在Java中，有一个较为特殊的类称为**Class类**，又叫作**元类**。所有其他Java类都会对应有一个Class对象用来**保存该类的信息**，如类名、字段名、方法名等等。另外，Class类同时又继承于Object类，Object依然是**所有**类的超类。

### Class实例对象

每个类都会有一个静态属性`class`，通过该属性就可以获取对应的Class对象：

```java
Class<String> clazz = String.class;

Class<?> clazz2 = Integer.class;
```



所有实例化对象都有一个`getClass()`成员方法，通过该方法可以获取相应的元类对象：

```java
String s = "abc";
Class<String> clazz = s.getClass(); //获得对象s的类（即java.lang.String）所对应的Class对象
```



使用Class类的静态方法`forName(String className)`来获取元类对象：

```java
Class<?> clazz = Class.forName("java.lang.String");

Class<?> clazz2 = Class.forName("io.virjid.Demo");
```

`forName(String className)`会根据全限定类名去获取相应的元类对象。如果`className`所对应的类没有被加载的话会先去执行加载操作，此时就有可能抛出`ClassNotFoundException`的异常。



### 唯一的Class对象

现在以不同的方式去获取`java.lang.String`对应的Class对象：

```java
public static void main(String[] args) throws ClassNotFoundException {
    String s1 = "abc";
    String s2 = new String("123");
    
    Class<?> clazz1 = s1.getClass();
    Class<?> clazz2 = s2.getClass();
    Class<?> clazz3 = String.class;
    Class<?> clazz4 = Class.forName("java.lang.String");
    
    System.out.println("clazz1==clazz2 : " + (clazz1 == clazz2));
    System.out.println("clazz1==clazz3 : " + (clazz1 == clazz3));
    System.out.println("clazz1==clazz4 : " + (clazz1 == clazz4));
}


/** 运行结果：
clazz1==clazz2 : true
clazz1==clazz3 : true
clazz1==clazz4 : true
**/
```



可以发现，通过不同方式去尝试获取同一个类对应的Class对象，结果都**指向同一个Class对象**。

### 获取类信息

通过Class对象就可以去获取对应的类的相关信息：

```java
public static void main(String[] args) {
    String a = "abc";
    
    System.out.println(a.getClass().getName());
    System.out.println(a.getClass().getSimpleName());
}

/** 运行结果：
java.lang.String
String
**/
```

上面的例子仅仅是获取了类的全限定名和简单名，通过Class对象当然还可以获取更多的信息，下面记录一些常用类信息的获取方式。



## 获取类的构造器

Class对象可以通过如下的成员方法来获取 **`Constructor对象`** ，即构造器对象：

| 方法                                                 | 用途                                   |
| ---------------------------------------------------- | -------------------------------------- |
| `getConstructor(Class...<?> parameterTypes)`         | 获得该类中与参数类型匹配的公有构造方法 |
| `getConstructors()`                                  | 获得该类的所有公有构造方法             |
| `getDeclaredConstructor(Class...<?> parameterTypes)` | 获得该类中与参数类型匹配的构造方法     |
| `getDeclaredConstructors()`                          | 获得该类所有构造方法                   |

通过构造器对象我们还可以对相应的类进行实例化操作。



```java
public static void main(String[] args) throws Exception {
    Class<?> clazz = String.class;
    
    Constructor<?> constructor = clazz.getConstructor(String.class);
    Object o = constructor.newInstance("hello"); //调用构造器实例化对象
    
    System.out.println(o);
}

/** 运行结果：
hello
**/
```



## 获取类的方法

Class对象可以通过如下的成员方法来获取 **`Method对象`** ，即方法对象：

| 方法                                                         | 说明                 |
| ------------------------------------------------------------ | -------------------- |
| `getMethod(String name, Class..<?> parameterTypes)`          | 获得该类某个公有方法 |
| `getMethods()`                                               | 获得该类所有公有方法 |
| `getDeclaredMethod(String name, Class...<?> parameterTypes)` | 获得该类某个方法     |
| `getDeclaredMethods()`                                       | 获得该类所有方法     |

通过Method对象的`invoke(Object obj, Object... args)`成员方法我们还可以调用对应的方法。

```java
public static void main(String[] args) throws Exception {
    Class<?> clazz = String.class;
    
    Method method = clazz.getMethod("length");
    System.out.println(method.invoke("hello"));
}

/** 运行结果：
5
**/
```



## 获取方法/构造器的参数

无论是Constructor对象还是Method对象，都有一下几个关于参数的方法：

| 方法                | 用途                                  |
| ------------------- | ------------------------------------- |
| `getParameterCount` | 获取参数个数                          |
| `getParameters`     | 获取所有参数（参数对应于Parameter类） |
| `getParameterTypes` | 获取所有参数的类型，即Class对象       |



## 获取类的字段

Class对象可以通过如下的成员方法来获取 **`Field对象`** ，即字段对象：

| 方法                            | 用途                   |
| ------------------------------- | ---------------------- |
| `getField(String name)`         | 获得某个公有的属性对象 |
| `getFields()`                   | 获得所有公有的属性对象 |
| `getDeclaredField(String name)` | 获得某个属性对象       |
| `getDeclaredFields()`           | 获得所有属性对象       |



## 获取注解信息

一个类的注解可以出现在很多地方，比如：

- 修饰方法/构造器
- 修饰方法/构造器的参数
- 修饰类/接口
- 修饰字段

注解对应于 **`Annotation类`** 。

Method对象、Constructor对象、Class对象、Field对象、Parameter对象都拥有以下几个成员方法：

| 方法                                            | 用途                                   |
| ----------------------------------------------- | -------------------------------------- |
| getAnnotation(Class<A> annotationClass)         | 返回该类中与参数类型匹配的公有注解对象 |
| getAnnotations()                                | 返回该类所有的公有注解对象             |
| getDeclaredAnnotation(Class<A> annotationClass) | 返回该类中与参数类型匹配的所有注解对象 |
| getDeclaredAnnotations()                        | 返回该类所有的注解对象                 |