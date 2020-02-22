## 目的

保持对象之间的联动和最终一致

## 例子代码

疫情还没过去, 生活还要继续, 最近从村里来到了上海打工, 好慌呀, 一千米之内三个确诊小区, 最近的 700 米, 回想一开始疫情开始抢口罩没抢过人家, 抢连花清瘟也没抢过, 双黄连口服液一醒来就没了, 好奇呀, 这就是定时看新闻不能及时获取信息的结果, 所以这里我向大家推荐新闻的自动推送强制提醒模式 -&gt; 观察者模式

比如我们有个新闻热词的汇总搜索功能, 当新闻的热词更新的时候会发送给订阅他的人

先定义观察者的通用接口\(对应 java 中的 java.util.Observer\):

```java
public interface Observer<S extends Observable<S, O, A>, O extends Observer<S, O, A>, A> {

  void update(S subject, A argument);
}
```

上面的类会根据被观察者的状态变更调用 update 方法.

被观察者的通用实现\(对应 java.util.Observable\):

```java
public abstract class Observable<S extends Observable<S, O, A>, O extends Observer<S, O, A>, A> {

  protected List<O> observers;

  public Observable() {
    this.observers = new CopyOnWriteArrayList<>();
  }

  public void addObserver(O observer) {
    this.observers.add(observer);
  }

  public void removeObserver(O observer) {
    this.observers.remove(observer);
  }

  public void notifyObservers(A argument) {
    for (O observer : observers) {
      observer.update((S) this, argument);
    }
  }
}
```

我们定义假设被观察者是新闻的热词:

```java
public class COVID19MessageObervable extends Observable<COVID19MessageObervable, PersonObserver,String> {
}
```

我们定义观察者是一个个人:

```java
@AllArgsConstructor
@Getter
public class PersonObserver implements Observer<COVID19MessageObervable, PersonObserver,String> {

    private String name;

    @Override
    public void update(COVID19MessageObervable subject, String argument) {
        System.out.println("你好" + name + ", 赶紧囤 " + argument + " 吧");
    }
}
```

调用例子:

```java
COVID19MessageObervable newHotWorld = new COVID19MessageObervable();
newHotWorld.addObserver(new PersonObserver("codog"));
newHotWorld.addObserver(new PersonObserver("兰兰"));
newHotWorld.notifyObservers("口罩 + 酒精");
newHotWorld.notifyObservers("清瘟胶囊");
newHotWorld.notifyObservers("双黄连口服液");
```

## 课后作业

1. 使用 java 自带了 Observable 和 Observer 重写文中的例子
2. 实现一个并发的 notifyObservers 实现, 并发执行 overservers 中的 update 方法
3. 当用户账号发生了系统扣款的情况, 需要给用户发送 APP 推送, 邮件, 短信, 请使用观察者模式和不使用观察者模式实现, 当这时候需求变更为还需要增加发送微信推送, 发送钉钉消息, 发送飞书通知, 请比较二种时间的测试范围不同和实现难度的不同



