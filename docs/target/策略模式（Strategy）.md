# 策略模式（Strategy）



定义了算法族，分别封装起来，让它们之间可以互相替换。

```java

```



## 实际应用

​	我们知道企业员工的薪资计算方式大体一致，但每个组织又有自己不同的地方，如工资项的种类不同，对于相同的工资项算法有所差异等等。

```java
//抽象出薪资计算器接口
public interface SalaryCalculator {
	//计算工资
	void calculate();
    //是否考勤
	boolean isAttendance();
}
//实现一个默认的薪资计算器
public class DefaultSalaryCalculator implements SalaryCalculator{

	@Override
	public void calculate() {
		doCalculate();
	}
	@Override
	public boolean isAttendance() {
		return true;
	}
}
//实现一个定制化的薪资计算器: 中关村薪资计算器
public class ZGCSalaryCalculator implements SalaryCalculator{

	@Override
	public void calculate() {
		doCalculate1();
	}
	@Override
	public boolean isAttendance() {
		return false;
	}
}
//薪资计算执行器
public class SalaryExecutor {

    //对象组合
	private SalaryCalculator salaryCalculator;
	public AttendanceExecutor(SalaryCalculator salaryCalculator) {
		this.salaryCalculator = salaryCalculator;
	}
	public void setSalaryCalculator(SalaryCalculator salaryCalculator) {
		this.salaryCalculator = salaryCalculator;
	}
    //委托
	public void execute() {
		salaryCalculator.calculate();
	}
}
//使用示例
public class SalaryCalculateDemo{
    public static void main(String[] args){

		SalaryExecutor salaryExecutor = new SalaryExecutor(new DefaultSalaryCalculator());
		salaryExecutor.execute();
		
		salaryExecutor.setSalaryCalculator(new ZGCSalaryCalculator());
		salaryExecutor.execute();
    }
}
```

​	上面的示例中，首先抽象出一个薪资计算器接口，把变化的部分抽象出来，定义抽象方法，也就是 calculate() 计算薪资的逻辑，这部分计算逻辑在不同的组织下会有所不同。然后定义N个类实现接口，复写抽象方法，也就是封装一个个计算逻辑（策略），最后再定义一个类，使用组合的方式实现薪资计算执行器 SalaryExecutor，SalaryExecutor 的构造方法需要一个薪资计算的具体实现，在使用时实例化想要的计算逻辑对象即可，而且可以随时更换策略（算法）。



应用场景:

​	在许多算法相似的情况下，将这些算法封装成一个一个的类，实现相同的接口，已达到相互替换的目的。这些算法就是一个个策略，因为实现了相同的接口，所以可以互相替换，这就是策略模式。



## 设计原则



设计原则1：

​	把变化的部分提取并封装起来，与其它不变的部分区分开来。

设计原则2：

​	针对接口编程，而不是针对实现编程；

设计原则3：

​	多用组合，少用继承。



