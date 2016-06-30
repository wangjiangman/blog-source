ArrayList 

之前初始化大小为 10

1.8 中初始化为空数组， 当添加第一个元素的时候，调整为 10.

```java
public ArrayList() {
    super();
    this.elementData = EMPTY_ELEMENTDATA;
}
```

```java
public void ensureCapacity(int minCapacity) {
    int minExpand = (elementData != EMPTY_ELEMENTDATA)
        // any size if real element table
        ? 0
        // larger than default for empty table. It's already supposed to be
        // at default size.
        : DEFAULT_CAPACITY;

    if (minCapacity > minExpand) {
        ensureExplicitCapacity(minCapacity);
    }
}
```
    

扩容

之前为 * 1.5 + 1

```java
int newCapacity = (oldCapacity * 3) / 2 + 1;
```

1.8 中 直接 * 1.5 

```java
int newCapacity = oldCapacity + (oldCapacity >> 1);
```

http://www.cnblogs.com/chenssy/p/3498468.html


http://annegu.iteye.com/blog/539465
