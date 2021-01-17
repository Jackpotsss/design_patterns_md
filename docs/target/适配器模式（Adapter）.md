# 适配器模式（Adapter）



## 类适配器



## 对象适配器



## 应用实例

并发包中  **RunnableAdapter** 的实现是适配器模式, 将一个Runnable包装成一个 Callable;

```java
    public static <T> Callable<T> callable(Runnable task, T result) {
        if (task == null) throw new NullPointerException();
        return new RunnableAdapter<T>(task, result);
    }   

	static final class RunnableAdapter<T> implements Callable<T> {
        final Runnable task;
        final T result;
        RunnableAdapter(Runnable task, T result) {
            this.task = task;
            this.result = result;
        }
        public T call() {
            task.run();
            return result;
        }
    }
```



