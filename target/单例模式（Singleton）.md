# 单例模式（Singleton）

​	单例模式是用来创建对象的设计模式，确保该类型有且只有一个对象被创建。根据对象创建的时机不同，单例可分为懒汉模式和饿汉模式。

## 饿汉模式

饿汉的意思是等不及，要立即创建对象，所以饿汉模式中单例对象在类加载时就创建好了。

```java
//饿汉单例
public class SingletonDemo {
	private static final SingletonDemo INSTANCE = new SingletonDemo();
	private SingletonDemo(){
	}
	public static SingletonDemo getInstance() {
		return INSTANCE;
	}
}
```

​	因为类加载过程是线程安全的，所以饿汉模式天然就是线程安全的写法；无论你是否使用这个对象的单例，单例对象都已经在应用启动后就准备好了，所以如果你不使用该单例对象或很晚才去使用单例对象，会造成资源不必要的浪费。



## 懒汉模式

相比饿汉立即创建单例对象的方式，懒汉则与之相反，懒汉是在获取单例对象时才去创建。

```java
//懒汉单例
public class SingletonDemo {
	private volatile static SingletonDemo INSTANCE;
	private SingletonDemo(){
	}
    //双重检查+互斥锁
	public static SingletonDemo getInstance() {
		if (INSTANCE==null) {
			synchronized (SingletonDemo.class) {
                if (INSTANCE==null) {
                    INSTANCE = new SingletonDemo();
                }
			}
		}
		return INSTANCE;
	}
}
```

​	与饿汉天然支持线程安全不同，懒汉的写法必须考虑到线程安全问题，这里使用了双重检查+互斥锁的方式保证线程安全。之所以这么写是为了规避一种情况：线程A和B同时判断单例对象为空，并准备创建对象，A持有类锁开始创建对象，B在临界区外等待，当A释放锁后，单例对象其实创建完毕，不需要B再去创建对象，所以B进入临界区后，再次判断单例对象是否创建，如果已经创建（volatile保证多线程同步的内存可见性），那么就不需要再次创建了。

​	双重检查+锁的逻辑只会在单例对象为空的情况下运行，一旦单例对象创建成功，获取单例对象只需要一次判断的逻辑即可，因此不存在效率问题。

## 枚举

​	此外还可以使用Java5提供的枚举类实现单例模式，枚举是一个语法糖，编译后其实就是一个常量类，而枚举所有的实例都是该类的对象，且是静态全局常量。

```java
//枚举单例
public enum SingletonEnum {
	INSTANCE;
}
```



## 破坏单例

​	Java编程中，创建对象不只有使用new关键字一种实现方式，还有可以使用**反射或反序列化**创建一个新的对象，所以在单例模式中同样应该要考虑到这种情况，为了防止单例模式被破坏，需要额外做一些工作。

**反射** 

​	即便是私有的构造器，通过反射技术也可以访问到并实例化一个新的对象。解决方案：加入一个全局静态变量当做标识，实例化对象时改变其状态，这样通过反射再次访问构造方法时，校验不通过，也就无法创建新的对象。

**反序列化** 

```java

```



## 应用实例

每一个Java应用只有一个Runtime对象，因此JDK中 java.lang.Runtime 类使用单例模式创建对象。

```java
public class Runtime {
    //饿汉创建单例
    private static Runtime currentRuntime = new Runtime();
    private Runtime() {}
    
    public static Runtime getRuntime() {
        return currentRuntime;
    }
}
```



