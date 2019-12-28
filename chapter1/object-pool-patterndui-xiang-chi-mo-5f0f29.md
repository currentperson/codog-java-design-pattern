## 目的

节省一组常用对象创建的耗时

## 例子代码

你的小黄车押金退了么? 共享经济这个词从小黄车和摩拜之争被大家所关注, 目前二家的创始人的境遇完全不同, 但辉煌时刻都是非常不一般的, 融资过亿, 街道到处都是二个车的影子, 而现在还存在着人们视野中的共享经济应该还有共享雨伞和共享充电宝了.

![](/assets/2019122801.png)

简单来讲共享经济其实就是使用了对象池模式, 所以说技术学得好, ~~要饭要到老~~\(数钱到手软\).

比如我们要从 A 地到 B 地, 最好的出行方式是自行车, 既能避免堵车又能锻炼身体, 但是我们没有自行车, 所以我们每次出行就造一辆自行车:

定义一个自行车类:

```java
@Data
public class Bicycle {

    private String colour;

    private Integer fee;

    private boolean broken;

    @SneakyThrows
    //造一辆自行车的代价
    public Bicycle(String colour, Integer fee, boolean broken) {
        Thread.sleep(1000);
        this.colour = colour;
        this.fee = fee;
        this.broken = broken;
    }
}
```

定义一个旅行操作:

```java
public class Travel {
    public static void fromA2BByBicycle() {
        System.out.println("from A to B by" + new Bicycle("yellow", 1, true));
    }
}
```

每次出行新造一个自行车, 出行 100 次:

```java
public static void main(String[] args) {
    for (int i = 0; i < 100; i++) {
        Travel.fromA2BByBicycle();
    }
}
```

## 池对象模式

先定义一个通用的对象池:

```java
public abstract class ObjectPool<T> {

    private Set<T> available = new HashSet<>();
    private Set<T> inUse = new HashSet<>();

    protected abstract T create();

    /**
     * Checkout object from pool.
     */
    public synchronized T checkOut() {
        if (available.isEmpty()) {
            available.add(create());
        }
        T instance = available.iterator().next();
        available.remove(instance);
        inUse.add(instance);
        return instance;
    }

    public synchronized void checkIn(T instance) {
        inUse.remove(instance);
        available.add(instance);
    }

    @Override
    public synchronized String toString() {
        return String.format("Pool available=%d inUse=%d", available.size(), inUse.size());
    }
}
```

集成这个类实现自行车的一个具体类:

```java
public class BicycleObjectPool extends ObjectPool<Bicycle> {

    @Override
    protected Bicycle create() {
        return new Bicycle("yellow", 1, true);
    }
}
```

使用自行车池来完成旅行:

```java
public static final BicycleObjectPool BICYCLE_OBJECT_POOL = new BicycleObjectPool();
    public static void fromA2BByBicycleFromPool() {
        Bicycle bicycle = BICYCLE_OBJECT_POOL.checkOut();
        System.out.println("" + bicycle);
        BICYCLE_OBJECT_POOL.checkIn(bicycle);
}
```

类图:

![](/assets/2019122802.png)

## 课后作业

1. 跑一下文中的二个程序, 对比耗时
2. 指出对象池模式的缺点















