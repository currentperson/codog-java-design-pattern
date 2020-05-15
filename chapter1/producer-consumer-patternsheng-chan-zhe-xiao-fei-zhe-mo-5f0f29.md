## 目的

效率型设计模式, 意义在于不阻塞主流程的同时完成异步操作, 比如发邮件短信, 完成耗时的任务等

## 例子代码

最近蜂巢收费 5 毛的事件引起了业主和物业的强烈反对, 5 毛钱其实也不是很多, 掉地上有人都不看一眼的, 也就我们这种中年男人才会捡起来揣兜里, 我们先来看看没有蜂巢的时候我们的快递其实是比较不方便的, 我们假设快递物品必须要交到收货人手里, 不然可能会有丢失风险.

先定义一个货物类:

```java
@Data
@AllArgsConstructor
//货物
public class Good {
    private String name;
}
```

比如我们定义一个消费者:

```java
@Data
@AllArgsConstructor
//'消费者'
public class Consumer {

    private String name;

    public void receiveGood(List<Good> goodList) {
        back2Home();
        System.out.println(name + "收到了" + goodList.toString());
    }

    //返回家中
    @SneakyThrows
    private void back2Home() {
        Thread.sleep(1000);
    }
}
```

定义一个快递小哥类:

```java
@Data
@AllArgsConstructor
//快递员
public class Courier {
    private String name;

    public void sendGood(Consumer consumer, List<Good> goodList) {
        consumer.receiveGood(goodList);
    }
}
```

快递交个得等每个顾客回家才能给下一个人送快递:

```java
StopWatch stopwatch = StopWatch.createStarted();
final Map<Consumer, List<Good>> DELIVERY_INFO_MAP = new HashMap<>();
DELIVERY_INFO_MAP.put(new Consumer("杰克马"), Arrays.asList(new Good("螃蟹"), new Good("河马")));
DELIVERY_INFO_MAP.put(new Consumer("奶茶妹妹"), Arrays.asList(new Good("奶茶")));
DELIVERY_INFO_MAP.put(new Consumer("小马哥"), Arrays.asList(new Good("肌肉")));
Courier courier = new Courier("脸盲东");
DELIVERY_INFO_MAP.forEach((k,v) -> {
    courier.sendGood(k,v);
});
stopwatch.stop();
System.out.println("total time: " + stopwatch.getTime(TimeUnit.SECONDS));
```

输出:

```java
杰克马收到了[Good(name=螃蟹), Good(name=河马)]
奶茶妹妹收到了[Good(name=奶茶)]
小马哥收到了[Good(name=肌肉)]
total time: 3
```

## 问题分析

快递小哥得等每个 '消费者' 回到家取快递, 太浪费时间了, 对顾客也不友好, 还得必须回到家取快递

## ~~丰巢模式~~\(生产者消费者模式\)

先定义一个消息\(货物\):

```java
@Data
public class EventItem<T> {

    private EventItemType eventType;

    private String eventId;

    private LocalDateTime eventDateTime;

    private T eventValue;
}
```

先定义一个丰巢\(消息队列\):

```java
public class ItemQueue {

    private BlockingQueue<EventItem> queue = new LinkedBlockingQueue<>(50);

    public void put(EventItem item) {
        try {
            queue.put(item);
        } catch (InterruptedException e) {
            throw new RuntimeException("put item to queue failed");
        }
    }

    public EventItem take() {
        try {
            return queue.take();
        } catch (InterruptedException e) {
            throw new RuntimeException("put item to queue failed");
        }
    }
}
```

再定义一个生产者:

```java
public class ItemProducer {

    private ItemQueue itemQueue;

    public ItemProducer(ItemQueue itemQueue) {
        this.itemQueue = itemQueue;
    }

    public <T> void generateEvent(EventItemType eventItemType, T value) {
        EventItem<T> eventItem = new EventItem<>();
        eventItem.setEventDateTime(LocalDateTime.now());
        eventItem.setEventId(UUID.randomUUID().toString());
        eventItem.setEventValue(value);
        eventItem.setEventType(eventItemType);
        itemQueue.put(eventItem);
    }
}
```

再定义一个生产者:

```java
public class ItemConsumer {

    private ItemQueue itemQueue;
    private EnumMap<EventItemType, ItemHander> typeItemHander;

    public ItemConsumer(ItemQueue itemQueue, EnumMap<EventItemType, ItemHander> typeItemHander) {
        this.itemQueue = itemQueue;
        this.typeItemHander = typeItemHander;
    }

    public void handleEvent() {
        EventItem eventItem = itemQueue.take();
        typeItemHander.get(eventItem.getEventType()).handle(eventItem);
    }
}
```

针对不同消息的处理器接口:

```java
public interface ItemHander<T> {
    void handle(EventItem<T> eventItem);
}
```

本次的具体实现类, 本次放入消息的是货物:

```java
@Data
@AllArgsConstructor
public class ConsumerItemHandler implements ItemHander<List<Good>> {

    private String name;

    @Override
    public void handle(EventItem<List<Good>> eventItem) {
        System.out.println(name + "收到了" + eventItem.getEventValue().toString());
    }
}
```

具体使用:

```java
StopWatch stopwatch = StopWatch.createStarted();
ItemQueue itemQueue = new ItemQueue();
new ItemProducer(itemQueue).generateEvent(EventItemType.SEND_GOOD_TO_JIEKEMA, Arrays.asList(new Good("螃蟹"), new Good("河马")));
new ItemProducer(itemQueue).generateEvent(EventItemType.SEND_GOOD_TO_NAICHA, Arrays.asList(new Good("奶茶")));
new ItemProducer(itemQueue).generateEvent(EventItemType.SEND_GOOD_TO_XIAOMAGE, Arrays.asList(new Good("奶茶")));

EnumMap<EventItemType, ItemHander> typeItemHander = new EnumMap<>(EventItemType.class);
typeItemHander.put(EventItemType.SEND_GOOD_TO_JIEKEMA,new ConsumerItemHandler("杰克马"));
typeItemHander.put(EventItemType.SEND_GOOD_TO_NAICHA,new ConsumerItemHandler("奶茶妹妹"));
typeItemHander.put(EventItemType.SEND_GOOD_TO_XIAOMAGE,new ConsumerItemHandler("小马哥"));
ItemConsumer itemConsumer = new ItemConsumer(itemQueue, typeItemHander);
new Thread(() -> {
    while (true) {
        itemConsumer.handleEvent();
    }
}).start();
Thread.sleep(1000);
stopwatch.stop();
System.out.println("total time: " + stopwatch.getTime(TimeUnit.SECONDS));
```

输出:

```
杰克马收到了[Good(name=螃蟹), Good(name=河马)]
奶茶妹妹收到了[Good(name=奶茶)]
小马哥收到了[Good(name=奶茶)]
total time: 1
```

类图如下:

![](/assets/2020051504.png)

## 课后作业

1. 把消息队列换成持久存储的方式
2. 同时异步发送邮件和短信二种通知, 做一个邮件和短信的处理器

## 广告时间

最近也没人打赏了, 就我妈每次打赏二十元,,, 要放个广告了

字节跳动招聘:

**java 开发社招:**

![](/assets/2020051500.png)

[https://job.toutiao.com/s/oFnSKT](https://job.toutiao.com/s/oFnSKT)

**前端开发社招:**

![](/assets/2020051501.png)

[https://job.toutiao.com/s/oFcxYL](https://job.toutiao.com/s/oFcxYL)

**实习生招聘:**

![](/assets/2020051502.png)

[https://job.toutiao.com/s/oFqfKo](https://job.toutiao.com/s/oFqfKo)

