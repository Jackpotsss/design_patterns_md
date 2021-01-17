# 迭代器模式（Iterator）



## 定义

​	迭代器提供一种方式顺序访问聚合对象中的每个元素，而又不暴露其内部实现。



## Java迭代器

有意思的是，在我刚刚接触Java的时候，没觉得迭代器是一种设计模式，而是把它当做一种理所应当的设计。

```java
//可迭代的接口
public interface Iterable<T> {
    Iterator<T> iterator();   
}
//迭代器
public interface Iterator<E> {
    boolean hasNext();
    E next();
    void remove();
}
```

​	java.util.Collection 接口继承了 Iterable 接口，所以Collection 的所有实现类都具有迭代器功能。下面看看 ArrayList 迭代器的具体实现。

```java
public class ArrayList<E> implements List<E>{
    public Iterator<E> iterator() {
        return new Itr();
    } 
    private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        public boolean hasNext() {
            return cursor != size;
        }
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size) throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length) throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }

        public void remove() {
            checkForComodification();
            try {
                ArrayList.this.remove(lastRet);
                cursor = lastRet;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }
    }
}
```

注：

​	Java中的集合种类有很多，从面向对象的角度来讲，它们都属于聚合对象，每个聚合对象的迭代器实现都不太相同，但思想却是一样的，都是为了提供顺序访问内部所有元素的方法，而又不暴露其内部实现。

​	ArrayList 内部的迭代器属于fail-fast 迭代器，即在迭代期间不允许修改集合内部元素的内容，否则迭代的内容和实时数据就会不一致，Java中很多集合都采用了这种方式，以保证数据安全。

​	Java的可迭代接口 Iterable 和接口方法 iterator() ，将实例化的具体迭代器延迟到子类中，由子类决定实例化哪个对象，这是典型的工厂方法模式。



