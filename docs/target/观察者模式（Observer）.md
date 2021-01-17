# 观察者模式（Observer）

​	观察者模式是非常常见的一种设计模式，它的应用场景很广泛。观察者模式中定义了两个角色：主题和订阅者，就像报刊和订阅人一样。多个订阅者可以订阅一个主题，主题一旦发生变化，就会通知所有的订阅者知晓，订阅者根据得到的信息作出自己的反应。

​	

## 简单栗子

​	主题是气象信息，订阅者是当前气象显示板。气象站一旦发布最新的气象信息（温度、湿度、气压），就会在气象显示板上立即更新。



```java
//订阅者
public interface Observer {

	void update(float temp,float humidity,float perssure);
}
//主题
public interface Subject {

	void registerObserver(Observer o);
	
	void removeObserver(Observer o);
	
	void notifyObservers();
}
```



```java
//主题:气象信息
public class WeatherData implements Subject {

	private List<Observer> observers;
	private float temp;//温度
	private float humidity;//湿度
	private float perssure;//气压

	public WeatherData() {
		observers = new ArrayList<Observer>();
	}

	@Override
	public void registerObserver(Observer o) {
		observers.add(o);
	}

	@Override
	public void removeObserver(Observer o) {
		observers.remove(o);
	}

	@Override
	public void notifyObservers() {
		for (Observer observer : observers) {
			observer.update(temp, humidity, perssure);
		}
	}

	public void setData(float temp, float humidity, float perssure) {
		this.temp = temp;
		this.humidity = humidity;
		this.perssure = perssure;
		notifyObservers();
	}
}
//订阅者:气象信息显示板
public class CurrentDisplay implements Observer{
	
	private float temp;
	private float humidity;
	private float perssure;
	private Subject subject;
	
	public CurrentDisplay(Subject subject) {
		this.subject = subject;
		subject.registerObserver(this);//自动订阅主题
	}

	@Override
	public void update(float temp, float humidity, float perssure) {
		this.temp = temp;
		this.humidity = humidity;
		this.perssure = perssure;
		display();
	}

	public void display() {
		System.out.println("CurrentDisplay [temp=" + temp + ", humidity=" + humidity + ", perssure=" +			 		perssure + "]");
	}
}
//测试
public class ObserverDemo {
	public static void main(String[] args) {
		WeatherData weatherData = new WeatherData();
		CurrentDisplay currentDisplay = new CurrentDisplay(weatherData);	
		weatherData.setData(12, 23, 33);
	}
}
```

## 定义





## 实际应用

JDK的Swing大量使用观察者模式，很多GUI 框架也是如此。



Observer 是观察者，Observable是主题，

```java
public interface Observer {
    void update(Observable o, Object arg);
}
```



## 设计原则

设计原则1：

​	找出程序中变化的部分，然后和固定不变的部分相分离。

​	在观察者模式中，主题的状态会发生变化，观察者的类型和数量也会发生变化，用这个模式，可以在不改变主题的情况下，改变依赖于主题的观察者对象。

设计原则2：

​	针对接口编程，不针对实现编程。

​	主题和观察者都使用接口，观察者利用主题接口向主题注册自己，主题利用观察者接口通知观察者，两者之间运作正常，又同时具有松耦合的优点。

设计原则3：

​	多用组合，少用继承。

​	观察者模式利用组合将许多观察者注册进主题中，对象之间的这种关系不是通过继承得到的，而是在运行时通过组合的方式产生的。



