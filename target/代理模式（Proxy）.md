# 代理模式（Proxy）



## 远程代理

Java RMI



## 虚拟代理



## 动态代理(保护)

Java动态代理也是在反射包中提供的功能，动态代理是一种基于反射，根据接口生成代理类（动态生成字节码）的技术。



### 使用

使用Java动态代理为目标类生成接口的代理类，首先**被代理的类必须实现接口**：

```java
public interface ISay {
	void sayHello();
	void sayBye();
}

public class Hello implements ISay {

	@Override
	public void sayHello() {
		System.out.println("hello...");
	}

	@Override
	public void sayBye() {
		System.out.println("sayBye...");	
	}
}
```

第二步，编写**调用处理器**，每一个代理实例都会关联一个调用处理器，生成代理实例需要用到：

```java
public class ProxyHandler implements InvocationHandler{
	
	private Object obj;

	public ProxyHandler(Object obj) {
		this.obj = obj;
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println(method.getName()+"before method invoke...");
		Object result = method.invoke(obj, args);
		System.out.println(method.getName()+"after  method invoke...");
		return result;
	}
}
```

第三步，生成代理类：

```java
public static void main(String[] args) {

	ISay hello = new Hello();
	ProxyHandler proxyHandler = new ProxyHandler(hello);
	ISay newProxyInstance = 		(ISay)Proxy.newProxyInstance(hello.getClass().getClassLoader(), 	hello.getClass().getInterfaces(), proxyHandler);
	newProxyInstance.sayHello();
	newProxyInstance.sayBye();
	
}
```

Proxy.newProxyInstance 方法需要传入3个参数：**类加载器、代理类的接口列表和代理类处理器**。

输出内容：

```
sayHello before method invoke...
hello...
sayHello after  method invoke...

sayBye before method invoke...
sayBye...
sayBye after  method invoke...
```

​	



## 其他变体





