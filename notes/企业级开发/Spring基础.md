# IoC 与 DI 的思想

## 交出控制权

这里有一位牙医，他拥有一个诊治病人的方法 `treatment` 。

```java
public class Dentist {
	public void treatment(Patient patient) {
		System.out.println("treatmenting by a dentist...:"+patient.getName());
	}
}
```
现在有一位病人向医生寻求帮助：


```java
public class Patient {
	private Dentist doctor = new Dentist();
    private String name;
	private String disease;
	
	public void askForHelp() {
		doctor.treatment(this);
	}
    
    // ... 省略 setter&getter 方法 ...
}
```

很明显，病人去看哪位医生完全是由他自己决定的，这会导致一些问题：

- 病人得的未必是牙疼，你去找牙医 ... ... 似乎有些强人所难
- 病人可能不太懂应该找什么医生比较合适



我们需要引入这么一个角色，他管理着各种各样的医生，并且能够决定为病人分配哪一位医生，也就是 Hospital 类，并且为了统一管理医生还需要引入一个 Doctor 接口。下面是所有的代码：

```java
public interface Doctor {
    void treatment(Patient patient);
}
```

```java
public class Dentist implements Doctor {
    public void treatment(Patient patient) {
		System.out.println("treatmenting by a dentist...:"+patient.getName());
	}
}
```

```java
public class EmergencyDoctor implements Doctor {
	public void treatment(Patient patient) {
		System.out.println("treatmenting by an emergency doctor... :"+patient.getName());
	}
}
```

```java
public class Patient {
	private Doctor doctor;
    private String name;
	private String disease;
	
	public void askForHelp() {
		doctor.treatment(this);
	}
    
    // ... 省略 setter&getter 方法 ...
}
```

```java
public class Hospital {
    public void allocateDoctor(Patient patient) {
        patient.setDoctor(new EmergencyDoctor());
    }
}
```

```java
public class Client {
    public static void main(String[] args) {
        Hospital hospital = new Hospital();
        Patient patient = new Patient();
        patient.setName("Virjid");
        
        hospital.allocateDoctor(patient);
        
        patient.askForHelp();
    }
}
```

从编码的角度来看，病人一开始拥有创建医生对象的控制权；自从引入了医院之后，病人失去了该控制权并转交到了医院手上，这就是所谓的**控制反转**：

- 控制指的是什么？：创建医生对象的控制权
- 反转了什么东西？：控制权从病人手上反转至医院手上



我们还可以换一种角度去理解。病人寻求帮助同样需要医生，此时医院为病人分配了一位医生。这个**分配**的行为可以说成是**注入**，即医院为病人”注入“了一位医生，这就是所谓的**依赖注入**。



## 控制反转与依赖注入

IoC 即 `Inversion of Control` （控制反转）， DI 即  `Dependency Injection` （依赖注入）。两者的关系现在一般有这几种说法：

- 两者是站在不同角度看待同一件事物的观点
- IoC 是官方的说法，DI 是民间的说法
- DI 是 IoC 的一种实现



## Spring 的 IoC 容器





# BeanFactory 与 ApplicationContext

## BeanFactory

在 Spring 中凡是可以被 Spring 容器实例化并进行管理的 Java 类被称作为 **Bean** 。相应地，Spring 提供了一个接口称为 **BeanFactory** ，很明显实现该接口的实现类是**能够从 Spring 容器中取得 Bean 对象的工厂类**。

BeanFactory 拥有众多实现类，在 `Spring 5.1.8.RELEASE` 中该接口又有一个 3 个直接子接口和 1 个直接实现类：

<img src="./pics/spring-beanfactory-1.png"/>



下面简单介绍一下这三个子接口的作用：

- `AutowireCapableBeanFactory` ：该接口的实现类拥有自动装配bean的能力
- `HierarchicalBeanFactory` ：通过该接口可以访问到父容器
- `ListableBeanFactory` ：通过该接口可以访问到容器中Bean的各种基本信息

Spring 有一个工厂实现类叫作 `XmlBeanFactory` ，它可以通过读取 XML 配置文件来初始化一个 Spring IoC 容器：

```java
Resource resource = new ClassPathResource("application.xml");
XmlBeanFactory xmlFactory = new XmlBeanFactory(resource);
```

`XmlBeanFactory` 间接地实现了 `AutowireCapableBeanFactory` 、 `HierarchicalBeanFactory` 、 `ListableBeanFactory` 这三个接口：

```java
// 通过 BeanFactory 接口来获取容器中的 Bean
Cat cat = xmlFactory.getBean(Cat.class);

// 通过 ListableBeanFactory 接口获取容器中 Bean 的数量
int count = xmlFactory.getBeanDefinitionCount(); 
```

## ApplicationContext

**ApplicationContext** 由 BeanFactory 派生而来并进行了封装与拓展，可以说 ApplicationContext 更方便、适用于实际应用的开发。

**`ClasspathXmlApplicationContext`** 是 ApplicationContext 的一个实现类，它能够从**类路径**下读取 XML 配置文件来初始化一个 IoC 容器：

```java
ApplicationContext ctx =
				new ClassPathXmlApplicationContext("application.xml");
```



**`FileSystemXmlApplication`** 则可以从**文件系统**中读取 XML 配置文件：

```java
ApplicationContext ctx =
				new FileSystemXmlApplicationContext("D:/application.xml");
```



## 父子容器



# Bean 的生命周期



# Bean 的装配

