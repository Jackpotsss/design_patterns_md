# 组合模式（Composite）

​	组合模式将对象以树形结构组织起来，从而表示“部分、整体”的层次结构。



## 简单栗子

企业的雇佣职员有上下级的层级关系，可以使用组合模式表示。

```java
//雇员
public class Employee {

	private String name;
	private int age;
	List<Employee> subordinates;//下属职员

	public Employee(String name, int age) {
		this.name = name;
		this.age = age;
		subordinates= new ArrayList<Employee>();
	}
	
	public void addSubordinate(Employee e) {
		subordinates.add(e);
	}
	
	public void removeSubordinate(Employee e) {
		subordinates.remove(e);
	}
	
	public List<Employee> getSubordinates(){
		return subordinates;
	}

	@Override
	public String toString() {
		return "Employee [name=" + name + ", age=" + age + "]";
	}
}
//测试
public class CompositeDemo {
	public static void main(String[] args) {
		
		Employee CEO = new Employee("jack", 50);
		Employee manager1 = new Employee("manager1", 35);
		Employee manager2 = new Employee("manager2", 39);
		CEO.addSubordinate(manager1);
		CEO.addSubordinate(manager2);
		
		Employee staff1 = new Employee("staff1", 30);
		Employee staff2 = new Employee("staff2", 23);
		Employee staff3 = new Employee("staff3", 32);
		manager1.addSubordinate(staff1);
		manager1.addSubordinate(staff2);
		manager2.addSubordinate(staff3);
		getAllEmployeeByThis(CEO);
	}
	
	public static void getAllEmployeeByThis(Employee employee) {
		System.out.println(employee.toString());
		List<Employee> subordinates = employee.getSubordinates();
		for (Employee e : subordinates) {
			getAllEmployeeByThis(e);
		}
	}
}
```



