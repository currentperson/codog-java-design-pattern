## 目的

优雅的控制不同的客户端访问服务端的次数

## 例子代码

DuangDuang 网 GUOQING 在五一抢夺公章, 竟然是因为知道老婆不懂节流模式?

夫妻股票占比超过 90%, 但是占比 8% 的股东们最近乐了, 夫妻打架每方都要拉拢他们来掌握公司大权, 看来是瞬间变成大股东了, 可惜当年融资的时候找我我没有同意, 不然不是发达了,,,

当然了, 我们把公章服务作为服务端体现出来, 把需要申请公章的人作为客户端抽象出来, 限制客户端的规则可以这么做:

```java
public interface RemoteService {
    //拿公章
    void takeOfficialSeal(String clientName);
}
```

客户端:

```java
public abstract class Client {
    protected String clientName;
    protected RemoteService remoteService;

    public abstract void action();

    public String getClientName() {
        return clientName;
    }

    public void setClientName(String clientName) {
        this.clientName = clientName;
    }

    public RemoteService getRemoteService() {
        return remoteService;
    }

    public void setRemoteService(RemoteService remoteService) {
        this.remoteService = remoteService;
    }
}
```

客户端实现类:

```java
public class GuoQingClient extends Client {

    @Override
    public void action() {
        System.out.println(this.clientName + "进入大门");
        remoteService.takeOfficialSeal(this.clientName);
    }
}
```

我们先不管远程服务是怎么实现的, 先在客户端来高并发大数据的调用一波:

```java
RemoteService remoteService = new DuangDuangRemoteService();
ExecutorService executorService = Executors.newFixedThreadPool(2);

executorService.execute(() -> {
    for (int i = 0; i < 3; i++) {
        GuoQingClient guoQingClient = new GuoQingClient();
        guoQingClient.setClientName("GUOQING");
        guoQingClient.setRemoteService(remoteService);
        guoQingClient.action();
        sleep();
    }
});
executorService.execute(() -> {
    for (int i = 0; i < 3; i++) {
        GuoQingClient guoQingClient = new GuoQingClient();
        guoQingClient.setClientName("OTHERS");
        guoQingClient.setRemoteService(remoteService);
        guoQingClient.action();
        sleep();
    }
});
executorService.shutdown();
```

如果在服务端不做限制呢, 那么很可能公章就被抢没了, 如何避免或者减少这种问题发生呢

我们的服务端可以这么写:

```java
public class DuangDuangRemoteService implements RemoteService {

    public DangDangRemoteService() {
        new Timer(true).schedule(new TimerTask() {
            @Override
            public void run() {
                ACTUAL_CLIENT_COUNT_PER_SECOND.put("GUOQING", new AtomicInteger(0));
                ACTUAL_CLIENT_COUNT_PER_SECOND.put("OTHERS", new AtomicInteger(0));
            }
        }, 0, 1000);
    }

    public static final Map<String, AtomicInteger> ALLOW_CLIENT_COUNT_PER_SECOND = new ConcurrentHashMap<>();

    static {
        ALLOW_CLIENT_COUNT_PER_SECOND.put("GUOQING", new AtomicInteger(0));
        ALLOW_CLIENT_COUNT_PER_SECOND.put("OTHERS", new AtomicInteger(2));
    }

    public static final Map<String, AtomicInteger> ACTUAL_CLIENT_COUNT_PER_SECOND = new ConcurrentHashMap<>();

    static {
        ACTUAL_CLIENT_COUNT_PER_SECOND.put("GUOQING", new AtomicInteger(0));
        ACTUAL_CLIENT_COUNT_PER_SECOND.put("OTHERS", new AtomicInteger(0));
    }

    @Override
    public void takeOfficialSeal(String clientName) {
        if (ACTUAL_CLIENT_COUNT_PER_SECOND.get(clientName).getAndIncrement()
        >= ALLOW_CLIENT_COUNT_PER_SECOND.get(clientName).get()) {
            return;
        }
        System.out.println(clientName + "成功抢夺一枚公章" + new Random().nextInt());
    }
}
```

输出:

```java
OTHERS进入大门
GUOQING进入大门
OTHERS成功抢夺一枚公章-262467487
GUOQING进入大门
OTHERS进入大门
OTHERS成功抢夺一枚公章-1638503022
GUOQING进入大门
OTHERS进入大门
```

## 问题分析

也能实现节流的要求, 就这个提供服务的类的功能由本来的提供服务又添加了服务校验的规则, 这个就不符合单一职责原则了, 而且要是规则变化的维度不一样, 就很难处理了, 比如改为 GUOQING 每次都是 0, 其他人在周六日是 0, 其他时期是 2, 类似的规则改动我们希望能够放到其他的类中做处理.

## 节流模式



