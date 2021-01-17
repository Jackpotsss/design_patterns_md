# 模板方法模式（Template Method）



## 简单栗子

​	如下，SuperClassTemplate 是一个抽象的父类模板，定义了一个算法 calculate()，且子类不可以修改算法的执行步骤（声明为final），算法分为三步，第一步和第三步需要子类实现，第二步则只能使用父类的计算逻辑。

```java
//抽象的父类模板
public abstract class SuperClassTemplate {

	final void calculate() {
		stepOne();
		stepTwo();
		stepThree();
	}
	protected abstract void stepOne();
	final void stepTwo() {
		doSomeing();
	}
	protected abstract void stepThree();
}
//子类实现算法中某的几个步骤
public class SubClassTemplate extends SuperClassTemplate{

	@Override
	protected void stepOne() {
		doSomeing0();
	}

	@Override
	protected void stepThree() {
		doSomeing1();
	}
}
```

**模板方法定义了一个算法的步骤，并允许子类为一个或多个步骤提供实现。** 

实际应用中，父类也不一定非得是抽象类，普通类也可以，使用抛异常的方式强制子类提供实现。

```java
//父类模板
public class SuperClassTemplate {

	final void calculate() {
		stepOne();
		stepTwo();
		stepThree();
	}
    //想正常使用calculate()算法,则必须让子类覆盖重写
	protected void stepOne(){
        throw new UnsupportedOperationException();
    }
	final void stepTwo() {
		doSomeing();
	}
	protected void stepThree(){
        throw new UnsupportedOperationException();
    }
}
```



## 定义

​	模板方法模式是在一个方法中定义一个算法骨架，而将一些步骤延迟到子类中。模板方法使得子类在不改变算法结构的情况下，重新定义算法的某些步骤。



模板方法模式，简单且实用。



​		**接口使得安全地增强类的功能成为可能。**如果使用抽象类来定义类型，那么程序员除了使用继承的手段来增强功能，再没有其他的选择了。这样的得到的类与包装类相比，功能更差，也更加脆弱。

​	但是通过对接口提供一个抽象的骨架实现类，可以把接口和抽象类的优点结合起来。



封装算法；如工资计算器，员工工资计算的大体逻辑相似，但每个企业都或多或少的有不同的地方。



Collections Framework 为每个重要的集合接口都提供了一个骨架实现,包括

AbstractCollection、AbstractSet、AbstractList 和 AbstractMap 。







## 模板方法和钩子



线程池中使用钩子函数



```java
public class ThreadPoolExecutor extends AbstractExecutorService {

    final void runWorker(Worker w) {
        Thread wt = Thread.currentThread();
        Runnable task = w.firstTask;
        while (task != null || (task = getTask()) != null) {
            beforeExecute(wt, task);
            task.run();
            afterExecute(task, thrown);
        }
    }
    
    protected void beforeExecute(Thread t, Runnable r) { }
    
    protected void afterExecute(Runnable r, Throwable t) { }
}

```



​	当子类必须提供算法中的某个方法或步骤的实现时，就是用抽象方法；如果算法的这部分是可选的，就用钩子。

​	钩子有很多用途，常用的有两种，钩子可以充当算法中可选的部分，子类可以选择实现，也可以置之不理。第二种就是程序运行时对算法的步骤做出反应和选择。



## 实际应用

### 应用一：AQS（JDK）

​	Java并发包中的队列同步器AQS采用的就是模板方法模式，想要实现一个自定义的同步组件、锁，需要实现Lock接口，而Lock接口方法的具体实现是由队列同步器来提供的，于是作者就设计了一个抽象的队列同步器，供实现者使用，通过继承这个抽象的队列同步器来实现自己的同步器。

​	父类定义算法步骤，acquire 独占式获取锁，方法声明为 final ，表示不允许子类修改。该算法可以看做三个步骤，其中tryAcquire 方法指定必须由子类实现，这里可以看到指定父类方法必须由子类实现不单单是声明为抽象方法这一种方法，还可以声明为普通的protected方法，并在方法中直接抛出异常。这样要想算法能够正常运行，子类则必须实现。

```java
public abstract class AbstractQueuedSynchronizer{
    
    public final void acquire(int arg) {
        if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg)){
            selfInterrupt();
        }
    }
    
    protected boolean tryAcquire(int arg) {
        throw new UnsupportedOperationException();
    }
    
    final boolean acquireQueued(final Node node, int arg) {
        //doSomething();
    }
}
```

NonfairSync 继承父类AQS，并复写tryAcquire方法。

```java
public class ReentrantLock implements Lock{
	private final Sync sync;
    
	static final class NonfairSync extends AbstractQueuedSynchronizer {
        
        protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }  
    }
}
```



### 应用二 业务模型的发布动作

​	OA或大多数办公软件，都有支持工作流审批的业务单据（请假单、报销单、采购订货单等），在工作流审批通过的时机定义一个发布逻辑。

```java
public class BusinessModel{

	//发布: release()定义了发布算法,子类不允许修改,只能外部调用
	public final boolean release() {
		if (!canRelease()) {
			return false;
		}    
        doRelease();
        updateWorkFlowState();
        afterRelease();
    }
    //钩子函数,用于控制是否可以发布
    protected boolean canRelease() {
        return true;
    }
    //允许子类重新定义发布逻辑,或添加发布逻辑
    protected boolean doRelease() {
        return doSomething();
    }
    //修改工作流状态,这步逻辑定义为私有,不允许修改
    private void updateWorkFlowState(){
        doWork();
    }
    //钩子函数,父类为空实现,子类可以选择实现或不实现,用于添加发布成功的后续业务逻辑
    protected void afterRelease() {
    }
}
```





