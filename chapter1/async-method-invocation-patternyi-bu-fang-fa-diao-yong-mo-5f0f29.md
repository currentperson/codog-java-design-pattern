## 目的

效率型设计模式, 意义在于不阻塞主流程的同时完成异步操作, 还能将异步操作的结果聚集回主线程

## 例子代码

最近 70 后绿地高管抢了 80 后男人的 90 后老婆的事情也闹得沸沸洋洋,  70 后谎称自己不孕不育然后没有采取安全措施导致这个 90 后怀孕了, 还要给 80 后老公一笔钱, 孩子归 80 后老公管,

![](/assets/2020052300.png)![](/assets/2020052301.png)![](/assets/2020052302.png)

我们通过代码来讲讲这是什么事情:

定义一个 woman 类, 这次用不了 girl 类了, 我真的够累了

```java
@Data
@AllArgsConstructor
public class Woman {
    private String name;
    private LocalDate birthday;
}
```

定义一个男人类:

```java
@Data
public class Man {
    private String name;
    private LocalDate birthday;
    private String childName;

    @SneakyThrows
    public String playWithWoman(Woman woman) {
        Thread.sleep(2000);
        return this.name.equals("陈军") ? "陈军的孩子" : "";
    }
}
```

使用一下绿帽大法:

```java
StopWatch stopwatch = StopWatch.createStarted();
Man manAfter80 = new Man();
manAfter80.setBirthday(LocalDate.parse("1980-01-01"));
manAfter80.setName("史睿生");
Man manAfter70 = new Man();
manAfter70.setBirthday(LocalDate.parse("1970-01-01"));
manAfter70.setName("陈军");
Woman woman = new Woman("张雨婷", LocalDate.parse("1990-01-01"));
playSequence(manAfter80, manAfter70, woman);
stopwatch.stop();
System.out.println("total time: " + stopwatch.getTime(TimeUnit.SECONDS));
```

其中的方法实现如下:

```java
private static void playSequence(Man manAfter80, Man manAfter70, Woman woman) {
    String childWith70 = manAfter70.playWithWoman(woman);
    manAfter80.playWithWoman(woman);
    manAfter80.setChildName(childWith70);
    System.out.println("manAfter80: " + manAfter80);
}
```

输出如下:

```java
manAfter80: Man(name=史睿生, birthday=1980-01-01, childName=陈军的孩子)
total time: 4
```

## 问题分析

这个代码没啥问题, 绿帽扣得死死的, 主要是女主这么高明的人怎么能串行 play 呢, 并行不可少

## 异步方法调用模式

```java
@SneakyThrows
private static void playParallel(Man manAfter80, Man manAfter70, Woman woman) {
    CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> manAfter70.playWithWoman(woman));
    manAfter80.playWithWoman(woman);
    manAfter80.setChildName(future.get());
    System.out.println("manAfter80: " + manAfter80);
}
```

输出如下:

```java
manAfter80: Man(name=史睿生, birthday=1980-01-01, childName=陈军的孩子)
total time: 2
```

## 课后作业

1. CompletableFuture.supplyAsync 第二个入参使用一下, 使用自定义的线程池
2. 再定义多个 man, 进行多人运动

## 广告时间

为了答谢大家的赞赏, 先给个赞赏同学大哥广告:









