## 目的

保护一个资源在同一时间只会有一个资源持有者访问/操作

## 例子代码

最近好久没玩吃鸡了, 手都生了, 每局都是第二名的我不想过度吹嘘的我刚\(fu\)枪\(di\)技能, 最近一局很气, 我带着队友刚到房区, 刚下车听到三个脚步声知道打不过, 开车就跑, 但是倒地的队友挡住了我的车, 导致一起落地成盒了, 有队友好烦呀

![](/assets/2020053000.png) ![](/assets/2020053001.png)

![](/assets/2020053002.png)   ![](/assets/2020053003.png)

在吃鸡里有一个空投, 里面有三级头三级甲和枪, 当大家一起去拿空投的物资的时候, 会发现明明三级头就在箱子里, 却被干啥啥不行, 舔包第一名的队友抢去, 而你可能只抢到了30 发子弹,,, 这似乎是个理所应当的事情, 箱子里就一个三级头, 当然只能一个人穿, 但是这其实是个很难的问题, 我们来模拟一下:

先定义一个空投:

```java
@Data
public class AirdropBox {
    //枪的名字
    private String gunName = "GZONE";

    //最近法规必须戴头盔了, 这个翻译是不对的
    private String level3Head = "三级头";

    //三级甲
    private String level3Armor = "三级甲";

    //吉利服可能是这么翻译的
    private String luckClothes = "吉利服";
}
```

再定义一个玩家类:

```
@Data
public class Player {
    private String gunName;
    private String level3Head;
    private String level3Armor;
    private String luckClothes;

}
```

我们定义一个玩家取装备的方法:

```java
@SneakyThrows
public void takeGoodsFromAirDrop(Player player, AirdropBox airdropBox) {
    Thread.sleep(randomMillis());
    player.setGunName(airdropBox.getGunName());

    Thread.sleep(randomMillis());
    player.setLevel3Armor(airdropBox.getLevel3Armor());

    Thread.sleep(randomMillis());
    player.setLevel3Head(airdropBox.getLevel3Head());

    Thread.sleep(randomMillis());
    player.setLuckClothes(airdropBox.getLuckClothes());

    airdropBox.setLevel3Armor(StringUtils.EMPTY);
    airdropBox.setLevel3Head(StringUtils.EMPTY);
    airdropBox.setGunName(StringUtils.EMPTY);
    airdropBox.setLuckClothes(StringUtils.EMPTY);
}
```

使用多线程为玩家取装备:

```java
AirdropBox airdropBox = new AirdropBox();
Player nbBoy = new Player();
Player ljBoy = new Player();
ExecutorService executorService = Executors.newFixedThreadPool(2);
App app = new App();
Future<?> nbSubmit = executorService.submit(() -> app.takeGoodsFromAirDrop(nbBoy, airdropBox));
Future<?> ljSubmit = executorService.submit(() -> app.takeGoodsFromAirDrop(ljBoy, airdropBox));
while (!nbSubmit.isDone() && !ljSubmit.isDone()) {
}
System.out.println("大神的装备:" + nbBoy.toString());
System.out.println("我的装备:" + ljBoy.toString());
executorService.shutdown();
```

输出如下:

```java
大神的装备:Player(gunName=GZONE, level3Head=null, level3Armor=三级甲, luckClothes=null)
我的装备:Player(gunName=GZONE, level3Head=三级头, level3Armor=三级甲, luckClothes=吉利服)
```

## 问题分析

以往的问题分析都是指扩展性这样的分析, 这就涉及到正确性的分析了, 一个明显的问题是多线程情况下如何保证只有一个线程能访问到这个箱子

## 互斥模式

```java
public synchronized void takeGoodsFromAirDrop(Player player, AirdropBox airdropBox)
```

把这个方法加上锁就能解决问题了

## 更近一步

上面虽然是能够解决多线程情况下一个资源的占用处理, 但是如果是分布式情况下, 一套代码部署到多个服务器, 这个 JVM 层级的锁就不行了, 所以可以使用分布式锁

我们使用模板方法模式和 Callback 模式结合:

```java
public interface LockCallback {

    boolean checkStatus(String uuid);

    void execute();
}
```

定义一个模板:

```java
public class LockExecuteTemplate {

    @Autowired
    private CuratorFramework lockClient;

    public void handLockProcess(LockCallback lockCallback, BusinessTypeLockEnum businessTypeLockEnum, String uuid) {
        InterProcessLock lock = new InterProcessMutex(lockClient, businessTypeLockEnum + uuid);
        try {
            Preconditions.checkArgument(lock.acquire(2000, TimeUnit.SECONDS));
        } catch (Exception e) {
            log.error("acquire lock failed",e);
            throw new RuntimeException("acquire lock failed", e);
        }
        try {
            boolean isRightStatus = lockCallback.checkStatus(uuid);
            if(isRightStatus) {
                lockCallback.execute();
            }
        } catch (Exception e) {
            log.error("execute lock failed",e);
            throw new RuntimeException("execute lock failed", e);
        } finally {
            try {
                lock.release();
            } catch (Exception e) {
                log.error("release lock failed",e);
                throw new RuntimeException("release lock failed", e);
            }
        }

    }
}
```

lockClient 是使用 zookeeper 实现的分布式锁, lockCallback 是定义的接口, 用户可以传入不同的实现, 业务类型和 uuid 就是唯一确定一个资源

