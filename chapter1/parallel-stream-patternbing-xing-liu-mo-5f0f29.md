## 目的

提高程序运行效率, 减少响应时间或者增加吞吐量

## 例子代码

最近罗\*祥, 福报厂P12 jf 引起了大家的关注, 二位都是因为私人生活问题, 搞的微博程序员天天加班加服务器, 让我等吃了一个又一个瓜, 真是犯了全天下男人都会犯的错, 我感觉我就不会犯这种错, 当然是~~因为我是个好男人~~主要是因为没钱, 没名, 身体也......

行了行了, 说远了, 我们还是可以从他们身上学习到这个时间管理大法的, ~~从而可以有时间多参加 '多人运动'~~

我们假设有这么一个 '太虚公子' 这个一个坏男孩, 要和三位女孩一起玩\(shui\)耍\(jiao\)

坏男孩类:

```java
@Data
@AllArgsConstructor
public class BadBoy {

    private String name;

    public void playWithGirls(List<Girl> girls) {
        for (Girl girl : girls) {
            girl.playWithBody(this);
        }
    }
}
```

女孩类:

```java
@Data
@AllArgsConstructor
public class Girl {

    private String name;

    @SneakyThrows
    void playWithBody(BadBoy badBoy) {
        System.out.println(name + "开始和" + badBoy.getName() + "玩");
        doSomethingMySelf();
        System.out.println(name + "玩完了");
    }

    private void doSomethingMySelf() throws InterruptedException {
        //'睡觉' 1 秒完事
        Thread.sleep(500);
        System.out.println(name + "自己吃饭, 化妆, 发微博");
        Thread.sleep(500);

    }
}
```

我们让 '太虚公子' 与三位女孩玩耍:

```java
StopWatch stopwatch = StopWatch.createStarted();
BadBoy lzx = new BadBoy("空虚公子");
lzx.playWithGirls(List.of(new Girl("周*青"), new Girl("花花董花花"), new Girl("张大*")));
stopwatch.stop();
System.out.println("total time: " + stopwatch.getTime(TimeUnit.SECONDS));
```

我们看下输出和总耗时:

```java
周*青开始和 空虚公子玩
周*青自己吃饭, 化妆, 发微博
周*青玩完了
花花董花花开始和 空虚公子玩
花花董花花自己吃饭, 化妆, 发微博
花花董花花玩完了
张大*开始和 空虚公子玩
张大*自己吃饭, 化妆, 发微博
张大*玩完了
total time: 3
```

## 问题分析

就是时间管理不太好, 三个女孩需要三秒, 那不是太慢了, 能不能三个女孩就花一秒多的时间就能行呢

## 并行流模式

我们定义一个时间管理大师坏男孩:

```java
@Data
@AllArgsConstructor
public class ParallelBadBoy {

    private String name;

    public void playWithGirls(List<Girl> girls) {
        girls.parallelStream().forEach(girl -> girl.playWithBody(this));
    }
}
```

Girl 增加一个与时间管理大师玩的多态方法:

```java
@Data
@AllArgsConstructor
public class Girl {

    private String name;

    @SneakyThrows
    void playWithBody(BadBoy badBoy) {
        System.out.println(name + "开始和" + badBoy.getName() + "玩");
        doSomethingMySelf();
        System.out.println(name + "玩完了");
    }

    @SneakyThrows
    void playWithBody(ParallelBadBoy badBoy) {
        System.out.println(name + "开始和" + badBoy.getName() + "玩");
        doSomethingMySelf();
        System.out.println(name + "玩完了");
    }

    private void doSomethingMySelf() throws InterruptedException {
        //'睡觉' 1 秒完事
        Thread.sleep(500);
        System.out.println(name + "自己吃饭, 化妆, 发微博");
        Thread.sleep(500);

    }
}
```

调用:

```java
StopWatch stopwatch = StopWatch.createStarted();
ParallelBadBoy lzx = new ParallelBadBoy("八爪鱼");
lzx.playWithGirls(List.of(new Girl("周*青"), new Girl("花花董花花"), new Girl("张大*")));
stopwatch.stop();
System.out.println("total time: " + stopwatch.getTime(TimeUnit.SECONDS));
```

输出:

```java
周*青开始和八爪鱼玩
花花董花花开始和八爪鱼玩
张大*开始和八爪鱼玩
花花董花花自己吃饭, 化妆, 发微博
张大*自己吃饭, 化妆, 发微博
周*青自己吃饭, 化妆, 发微博
花花董花花玩完了
张大*玩完了
周*青玩完了
total time: 1
```

## 更近异步

如果不自定义线程池, 默认的是一个公有的线程池, 坏男孩太多了反而可能比串行还慢

```java
@Data
@AllArgsConstructor
public class ParallelBadBoy {
    private static final ForkJoinPool COLUMN_POOL = new ForkJoinPool(30);

    private String name;

    public void playWithGirls(List<Girl> girls) {
        COLUMN_POOL.submit(() -> girls.parallelStream().forEach(girl -> girl.playWithBody(this)));
    }
}
```

## 课后作业

1. 把 BadBoy 和 ParallelBadBody 抽象成一个类
2. 思考并行流一定可以加快速度么



