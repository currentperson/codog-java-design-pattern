## 目的

维护一起请求读的一致性, 避免一起请求读取相同的数据但是得到不同的结果, 相比于互斥锁有更好的并发

## 例子代码

最近福报厂的价值观又上了热搜, 微博因为蒋某热榜停更一周, 一个 P8 也试图以权谋色, 重演绿地70后高管的套路, 然后被开除了, 二个相似的对比不禁让人感慨, 员工触犯红线, 员工消失, 高管触犯红线, 红线消失.

比如我们有这样一个存储结构里面有福报厂最近的大瓜可以吃

```java
public class BadEventPool {

    public static final List<BadEvent> badEventList = new CopyOnWriteArrayList<>();

    public static String findNewBadEventPeopleName() {
        return badEventList.stream().max(Comparator.comparing(BadEvent::getHappenedTime))
            .orElse(new BadEvent()).getPeopleName();
    }

    public static String findNewBadEventName() {
        return badEventList.stream().max(Comparator.comparing(BadEvent::getHappenedTime))
            .orElse(new BadEvent()).getEventName();
    }
}
```

每个大瓜的结构如下:

```java
@NoArgsConstructor
@AllArgsConstructor
@Getter
@ToString
public class BadEvent {

    private String peopleName;

    private String eventName;

    private LocalDateTime happenedTime;
}
```

我们试图输出最新的大瓜:

```java
Thread runnable = new Thread() {
    @Override
    public void run() {
        IntStream.range(0,100).forEach(
            (i) ->
            {
                try {
                    sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                BadEvent p8 = new BadEvent("无名P8",
                    "对应聘人员语言骚扰, 触碰红线, 员工消失", LocalDateTime.now());
                BadEvent jf = new BadEvent("蒋某", "出轨网红, 触碰红线, 红线消失",
                    LocalDateTime.now());
                BadEvent badEvent = i < 50 ?
                    jf
                    : p8;
                //System.out.println(badEvent);
                BadEventPool.badEventList.add(badEvent);
            }
        );
    }
};
runnable.start();

sleep(2000);
String newBadPeopleName = BadEventPool.findNewBadEventPeopleName();
sleep(5000);
String newBadEventName = BadEventPool.findNewBadEventName();
System.out.println("result: " + newBadPeopleName + newBadEventName);
```

输出如下:

```
result: 蒋某对应聘人员语言骚扰, 触碰红线, 员工消失
```

## 问题分析

主要是一个线程在查询的过程中, 另外一个线程把最新的大瓜的变掉了, 所以导致了这个情况, 也是就数据库隔离级别中的可重复读类似的没有保证才会重现这种谣言.

## 读写锁模式

```java
ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
ReadLock readLock = lock.readLock();
WriteLock writeLock = lock.writeLock();
Thread runnable = new Thread() {
    @Override
    public void run() {
        writeLock.lock();
        IntStream.range(0,100).forEach(
            (i) ->
            {
                try {
                    sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                BadEvent p8 = new BadEvent("无名P8",
                    "对应聘人员语言骚扰, 触碰红线, 员工消失", LocalDateTime.now());
                BadEvent jf = new BadEvent("蒋凡", "出轨网红, 触碰红线, 红线消失",
                    LocalDateTime.now());
                BadEvent badEvent = i < 50 ?
                    jf
                    : p8;
                //System.out.println(badEvent);
                BadEventPool.badEventList.add(badEvent);
            }
        );
        writeLock.unlock();
    }
};
runnable.start();

sleep(2000);
readLock.lock();
String newBadPeopleName = BadEventPool.findNewBadEventPeopleName();
sleep(5000);
String newBadEventName = BadEventPool.findNewBadEventName();
readLock.unlock();
System.out.println("result: " + newBadPeopleName + newBadEventName);
```

输出结果如下:

```java
result: 无名P8对应聘人员语言骚扰, 触碰红线, 员工消失
```

## 课后作业

1. 将 jvm 锁改成分布式读写锁
2. 使用 try-with-resource 方式实现读写锁的释放



