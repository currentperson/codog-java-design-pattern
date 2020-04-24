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



