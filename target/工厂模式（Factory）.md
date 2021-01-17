# 工厂模式（Factory）

​	工厂模式属于创建型模式，用于创建对象，使用工厂模式，客户端获取的新对象不是由自己创建，而是由工厂负责创建，已达到将对象的创建过程封装的目的。

## 简单工厂

​	简单工厂其实不是一种设计模式，而是一种编程习惯，将创建对象的代码从客户代码中剥离出去。

### 应用实例

java.util.concurrent包中的 Executors  是创建任务执行器（线程池）的工具类，定义了创建线程池的静态工厂方法。

```java
//静态工厂
public class Executors {
    
    public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, ...);
    }
    
    public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1, ...);
    }   
    public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }
}
//普通工厂

```



注：使用静态工厂，可以不用创建对象就可以使用，但也有弊端，就是无法使用对象的继承来扩展方法。



## 工厂方法模式（Factory Method）

​	工厂方法用来处理对象的创建，并将创建对象的代码封装在子类中，这样客户程序中关于超类的代码就和子类创建对象的代码解耦了。



### 简单栗子

​	某披萨全国连锁商店生产披萨，各个省市的披萨商店所生产的披萨种类不一样，生产过程也有差异。这种场景我们可以使用工厂方法来生产披萨。

```java
//创建者: 定义生产披萨的工厂方法
public abstract class PizzaStore {
	
	public Pizza orderPizza(String name) {
		Pizza pizza = createPizza(name);
		pizza.prepare();
		pizza.bake();
		pizza.cut();
		pizza.box();
		return pizza;
	}
	//将创建对象的代码抽象
	protected abstract Pizza createPizza(String name);
}

//产品: 披萨超类
public abstract class Pizza {
	private String name;
	public Pizza(String name) {
		this.name=name;
	}
	public abstract void prepare();
	public abstract void bake();
	public abstract void cut();
	
	public void box() {
		System.out.println("包装披萨...");
	};
	public String getName() {
		return name;
	}
}
```

创建具体产品的代码封装到子类中，这样客户端只需要使用产品的抽象就可以获取产品，而不需要知道具体的产品是什么。

```java
//披萨工厂的具体实现:德州披萨商店
public class DeZhouPizzaStore extends PizzaStore{

	@Override
	protected Pizza createPizza(String name) {
		return new CheesePizza(name);
	}
}
//披萨的具体实现:奶酪披萨
public class CheesePizza extends Pizza {

	public CheesePizza(String name) {
		super(name);
	}
	@Override
	public void prepare() {
		System.out.println("准备芝士和奶油");
	}

	@Override
	public void bake() {
		System.out.println("烘焙15分钟");
	}

	@Override
	public void cut() {
		System.out.println("横着切");
	}
}
//测试
public class PizzaStoreDriver {
	public static void main(String[] args) {
		DeZhouPizzaStore deZhouPizzaStore = new DeZhouPizzaStore();
		ShangHaiPizzaStore shangHaiPizzaStore = new ShangHaiPizzaStore();
		
		deZhouPizzaStore.orderPizza("deZhou");
		shangHaiPizzaStore.orderPizza("shangHai");
	}
}
```



### 定义

工厂方法模式定义了一个创建对象的接口，由子类决定要创建的类是哪一个，工厂方法让类把实例化推迟到子类中完成。

工厂方法包含四个角色：

- 创建者
- 具体创建者（子类）
- 抽象产品
- 具体产品


注：

​	工厂方法和创建者并不一定总是抽象的，定义一个默认的工厂方法来产生具体的产品，这样即使创建者没有任何子类，依然可以创建产品。

**简单工厂和工厂方法** 

​	工厂方法是简单工厂的进一步抽象，在工厂方法模式中，核心的工厂类不再负责所有产品的创建，而是将具体的创建工作交给子类去做。这个核心工厂主要负责声明具体工厂所要实现的接口，而不负责创建对象，这使得工厂方法可以在不修改工厂角色的情况下引进新产品。符合“对拓展开发，对修改关闭” 的设计原则。



### 设计原则

依赖倒置原则

要依赖抽象，不要依赖具体类。





## 抽象工厂模式（Abstract Factory）

​	工厂方法模式通过引入工厂等级结构（父子关系），解决了简单工厂中工厂类职责太重的问题。但工厂方法的每个工厂只能生产一类产品，如果产品种类很多，可能会导致系统中存在大量的工厂类，





**工厂方法和抽象工厂** 

工厂方法和抽象工厂都是负责创建对象，工厂方法通过类的继承，抽象工厂通过对象的组合；







