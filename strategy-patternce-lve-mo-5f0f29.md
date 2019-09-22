## 目的

做一件事情有不同的实现方式, 可以将变化的部分和不变得部分剥离开, 去除大量的 if/else, 提供高扩展性

## 例子代码

比如我们要带妹吃鸡, 就要成为一个神枪手, 在各种枪战游戏中, 有各种不同的枪, 我们要根据实际情况\(比如射程\)的不同选择不同的枪进行射击, 我们可以假设每把枪的子弹数量都不富裕, 我们要用最合适的方法, 最少的子弹完成最强的伤害, 最终大吉大利, 今晚就可以想带妹子去哪里吃鸡都可以啦, 真的是哪里都可以哦

比如我们距离对手在一米内的时候使用平底锅\( 想我当时三级头三级甲, 手持 AKM, 满血满状态, 三级包里药包无数, 到了决赛圈被平底锅堵在墙角打死啦 \), 距离 100 米的时候使用冲锋枪, 超过1000 米使用狙击枪\(基本流程是开一枪没打中, 暴露位置, 被别人一狙打死\)

```java
/**
 * 面条式代码判断最强武器
 */
public class NoodlesKillProcessor {

    /**
     * 根据距离判断最好的武器击杀对手
     * @param distance
     */
    @BadSmell
    public static void killByDistance(int distance) {
        if(distance < 0) {
            throw new RuntimeException("距离咋还能是负数呢? 我以为只要我能 -20 cm 呢");
        }

        if(distance >= 0 && distance < 1) {
            System.out.println("发现敌人");
            System.out.println("二步快速走过去");
            System.out.println("掏出平底锅呼死他");
            return;
        }
        if(distance >= 1 && distance < 10) {
            System.out.println("发现敌人");
            System.out.println("快速走过去");
            System.out.println("掏出手枪打死他");
            return;
        }
        if(distance >= 10 && distance < 100) {
            System.out.println("发现敌人");
            System.out.println("身体站直, 心态稳住");
            System.out.println("掏出冲锋枪打死他");
            return;
        }
        if(distance >= 100 && distance < 1000) {
            System.out.println("发现敌人");
            System.out.println("身体蹲下降低后坐力");
            System.out.println("掏出步枪");
            System.out.println("打开 3 倍镜");
            System.out.println("开枪射击");
            return;
        }
        if(distance >= 1000) {
            System.out.println("发现敌人");
            System.out.println("趴在草丛里苟着");
            System.out.println("掏出狙击枪");
            System.out.println("打开 8 倍镜");
            System.out.println("开枪射击");
            return;
        }
    }
}
```

## 问题分析

我们觉得这有三个问题

一个是可读性问题, 我看这么多 if/else 语句, 里面的 sout 语句目前三四行也还好, 如果我们有上百行的语句, 里面也有很多 if/else, 这样都不知道下个主 if 跑哪去啦.

第二个是重复性问题, 全都需要发现敌人, 如果发现敌人是个成百上千行代码, 就很麻烦啦.

第三个是可维护性问题, 如果这时候我们新增了一种枪, 比如是散弹枪, 适用10 到20 的时候使用, 这时候我们就需要在加一个 if 语句如下:

```java
/**
 * 面条式代码判断最强武器
 */
public class NoodlesKillProcessor {

    /**
     * 根据距离判断最好的武器击杀对手
     * @param distance
     */
    @BadSmell
    public static void killByDistance(int distance) {
        if(distance < 0) {
            throw new RuntimeException("距离咋还能是负数呢? 我以为只要我能 -20 cm 呢");
        }

        if(distance >= 0 && distance < 1) {
            System.out.println("发现敌人");
            System.out.println("二步快速走过去");
            System.out.println("掏出平底锅呼死他");
            return;
        }
        if(distance >= 1 && distance < 10) {
            System.out.println("发现敌人");
            System.out.println("快速走过去");
            System.out.println("掏出手枪打死他");
            return;
        }
        if(distance >= 10 && distance < 20) {
            System.out.println("发现敌人");
            System.out.println("身体站直, 瞄准");
            System.out.println("打一枪算一枪");
            return;
        }
        if(distance >= 20 && distance < 100) {
            System.out.println("发现敌人");
            System.out.println("身体站直, 心态稳住");
            System.out.println("掏出冲锋枪打死他");
            return;
        }
        if(distance >= 100 && distance < 1000) {
            System.out.println("发现敌人");
            System.out.println("身体蹲下降低后坐力");
            System.out.println("掏出步枪");
            System.out.println("打开 3 倍镜");
            System.out.println("开枪射击");
            return;
        }
        if(distance >= 1000) {
            System.out.println("发现敌人");
            System.out.println("趴在草丛里苟着");
            System.out.println("掏出狙击枪");
            System.out.println("打开 8 倍镜");
            System.out.println("开枪射击");
            return;
        }
    }
}
```

这个看着也不没啥大问题的样子, 不就是加了个 if 么, 但是由于我们改动了这个文件, 测试同学问我们需要测试哪些功能, 说是测一种枪需要 5 天.

问题来啦, 本来说是你增加一种枪, 需要测 5 天, 但是现在你说改了这文件, 上下可能有些局部变量共享的, 或者有些方法可能改了入参的值, 这些有负作用的方法被调用啦, 所以可能狙击枪也得测一测, 可能手枪也得测一测.

测试同学崩了, 本来 5 天的工作量, 搞成了 5 \* 6 天, 一个月都在测枪, KPI 都没啦, OKR 都没啦, 钱都没啦, 老婆都跑啦, 自己都绿啦等等等, 不拿刀砍你就是真感情啦

## 初步尝试解决

我们先定义好一个基础类, 解决一下可读性问题和重复性问题

定义一个基础武器类

```java
/**
 * 抽象的枪
 */
public abstract class Weapon {

    /**
     * 发现敌人
     */
    protected void findEnemy() {
        System.out.println("发现敌人");
    }

    /**
     * 开枪前的动作
     */
    protected abstract void preAction();

    /**
     * 开枪
     */
    protected abstract void shoot();

    /**
     * 杀人的动作
     */
    public void kill() {
        findEnemy();
        preAction();
        shoot();
    }
}
```

逐个实现武器的具体类, 平底锅, 冲锋枪, 步枪等类如下:

```java
/**
 * 平底锅
 */
public class Pan extends Weapon {

    @Override
    protected void preAction() {
        System.out.println("二步快速走过去");
    }

    @Override
    protected void shoot() {
        System.out.println("掏出平底锅呼死他");
    }
}
```

```java
/**
 * 手枪类
 */
public class Pistol extends Weapon {
    @Override
    protected void preAction() {
        System.out.println("快速走过去");
    }

    @Override
    protected void shoot() {
        System.out.println("掏出手枪打死他");
    }
}
```

```java
/**
 * 散弹枪
 */
public class Shotgun extends Weapon {

    @Override
    protected void preAction() {
        System.out.println("身体站直, 瞄准");
    }

    @Override
    protected void shoot() {
        System.out.println("打一枪算一枪");
    }
}
```

```java
/**
 * 狙击枪
 */
public class SniperRifle extends Weapon {

    @Override
    protected void preAction() {
        System.out.println("趴在草丛里苟着");
        System.out.println("掏出狙击枪");
        System.out.println("打开 8 倍镜");
    }

    @Override
    protected void shoot() {
        System.out.println("开枪射击");
    }
}
```

```java
/**
 * 冲锋枪
 */
public class SubmachineGun extends Weapon {

    @Override
    protected void preAction() {
        System.out.println("身体站直, 心态稳住");
    }

    @Override
    protected void shoot() {
        System.out.println("掏出冲锋枪打死他");
    }
}
```

我们的方法就可以改动得更清晰啦

```java
/**
 * 抽象出类代码判断最强武器
 */
public class WeaponKillProcessor {

    /**
     * 根据距离判断最好的武器击杀对手
     *
     * @param distance
     */
    @BadSmell
    public static void killByDistance(int distance) {
        if (distance < 0) {
            throw new RuntimeException("距离咋还能是负数呢? 我以为只要我能 -20 cm 呢?");
        }
        Weapon weapon = null;

        if (distance >= 0 && distance < 1) {
            weapon = new Pan();
        } else if (distance >= 1 && distance < 10) {
            weapon = new Pistol();
        } else if (distance > 10 && distance < 20) {
            weapon = new Shotgun();
        } else if (distance >= 20 && distance < 100) {
            weapon = new SubmachineGun();
        } else if (distance >= 100 && distance < 1000) {
            weapon = new Rifle();
        } else if (distance >= 1000) {
            weapon = new SniperRifle();
        }
        weapon.kill();
    }
}
```

类图如下:![](/assets/WeaponKillProcessor.png)

## 使用策略模式

上面的代码没有解决最根本的问题, 也就是去除 if/else, 所用的方法其实就是将 if else 转换为 for, 这样的代码后续添加枪就不需要再增加新的类型啦.

我们先定义一个通用的策略模式接口如下:

```java
/**
 * 策略模式
 */
public interface Strategy<T extends AbstractStrategyRequest, R extends AbstractStrategyResponse> {
    /**
     * 执行策略
     * @param request
     * @return
     */
    R executeStrategy(T request);
}
```

入参和出参都是基本的抽象类

```java
/**
 * 策略模式抽象入参
 */
public abstract class AbstractStrategyRequest {
}
```

```java
/**
 * 策略模式抽象出参
 */
public abstract class AbstractStrategyResponse {
}
```

实现一个武器抽象类实现接口

```java
public abstract class WeaponStrategy implements Strategy<WeaponStrategyRequest, AbstractStrategyResponse> {

    /**
     * 发现敌人
     */
    protected void findEnemy() {
        System.out.println("发现敌人");
    }

    /**
     * 开枪前的动作
     */
    protected abstract void preAction();

    /**
     * 开枪
     */
    protected abstract void shoot();

    /**
     * 获取距离范围
     * @return
     */
    protected abstract Range<Integer> queryDistanceRange();

    /**
     * 杀人的动作
     */
    public void kill() {
        findEnemy();
        preAction();
        shoot();
    }

    @Override
    public AbstractStrategyResponse executeStrategy(WeaponStrategyRequest request) {
        System.out.println("距离敌人 " + request.getDistance());
        kill();
        return null;
    }
}
```

其中的 Range 类实现如下:

```java
/**
 * 范围类
 * @param <T>
 */
@Data
@AllArgsConstructor
public class Range<T extends Comparable<T>> {

    private T start;

    private T end;

    public Range(T start, T end) {
        this.start = start;
        this.end = end;
    }

    private boolean isIncludeStart = true;

    private boolean isIncludeEnd = false;

    /**
     * 判断是否在范围内
     * @param target
     * @return
     */
    public boolean inRange(T target) {
        if(isIncludeStart) {
            if(start.compareTo(target) > 0) {
                return false;
            }
        } else {
            if(start.compareTo(target) >= 0) {
                return false;
            }
        }
        if(isIncludeEnd) {
            if(end.compareTo(target) < 0) {
                return false;
            }
        } else {
            if(end.compareTo(target) <= 0) {
                return false;
            }
        }
        return true;
    }
}
```

依次实现这个抽象武器策略类

```java
/**
 * 平底锅
 */
public class PanStrategy extends WeaponStrategy {

    @Override
    protected void preAction() {
        System.out.println("二步快速走过去");
    }

    @Override
    protected void shoot() {
        System.out.println("掏出平底锅呼死他");
    }

    @Override
    protected Range<Integer> queryDistanceRange() {
        return new Range<>(0, 1);
    }
}
```

```java
/**
 * 手枪类
 */
public class PistolStrategy extends WeaponStrategy {
    @Override
    protected void preAction() {
        System.out.println("快速走过去");
    }

    @Override
    protected void shoot() {
        System.out.println("掏出手枪打死他");
    }

    @Override
    protected Range<Integer> queryDistanceRange() {
        return new Range<>(1, 10);
    }
}
```

```java
/**
 * 步枪
 */
public class RifleStrategy extends WeaponStrategy {

    @Override
    protected void preAction() {
        System.out.println("身体蹲下降低后坐力");
        System.out.println("掏出步枪");
        System.out.println("打开 3 倍镜");
    }

    @Override
    protected void shoot() {
        System.out.println("开枪射击");
    }

    @Override
    protected Range<Integer> queryDistanceRange() {
        return new Range<>(100, 1000);
    }
}
```

```java
/**
 * 散弹枪
 */
public class ShotgunStrategy extends WeaponStrategy {

    @Override
    protected void preAction() {
        System.out.println("身体站直, 瞄准");
    }

    @Override
    protected void shoot() {
        System.out.println("打一枪算一枪");
    }

    @Override
    protected Range<Integer> queryDistanceRange() {
        return new Range<>(10, 20);
    }
}
```

```java
public class SniperRifleStrategy extends WeaponStrategy {

    @Override
    protected void preAction() {
        System.out.println("趴在草丛里苟着");
        System.out.println("掏出狙击枪");
        System.out.println("打开 8 倍镜");
    }

    @Override
    protected void shoot() {
        System.out.println("开枪射击");
    }

    @Override
    protected Range<Integer> queryDistanceRange() {
        return new Range<>(1000, Integer.MAX_VALUE);
    }
}
```

```java
/**
 * 冲锋枪
 */
public class SubmachineGunStrategy extends WeaponStrategy {

    @Override
    protected void preAction() {
        System.out.println("身体站直, 心态稳住");
    }

    @Override
    protected void shoot() {
        System.out.println("掏出冲锋枪打死他");
    }

    @Override
    protected Range<Integer> queryDistanceRange() {
        return new Range<>(20, 100);
    }
}
```

定义一个上下文类来对入参进行路由

```
/**
 * 策略上下文, 用来路由策略
 */
public class StrategyContext {

    public static final List<WeaponStrategy> WEAPON_STRATEGYS = new ArrayList<>();

    static {
        WEAPON_STRATEGYS.add(new PanStrategy());
        WEAPON_STRATEGYS.add(new PistolStrategy());
        WEAPON_STRATEGYS.add(new RifleStrategy());
        WEAPON_STRATEGYS.add(new ShotgunStrategy());
        WEAPON_STRATEGYS.add(new SniperRifleStrategy());
        WEAPON_STRATEGYS.add(new SubmachineGunStrategy());
    }

    public static void execute(Integer distance) {
        WEAPON_STRATEGYS.stream().filter((weaponStrategy -> {
            Range<Integer> integerRange = weaponStrategy.queryDistanceRange();
            return integerRange.inRange(distance);
        })).findAny().get().executeStrategy(new WeaponStrategyRequest(distance));
    }
}
```

最后在主方法里面调用就好啦

```java
public class App {

    public static void main(String[] args) {
        int distance = 89;
        //NoodlesKillProcessor.killByDistance(distance);
        StrategyContext.execute(89);
    }
}
```

结果如下:

```
距离敌人 89
发现敌人
身体站直, 心态稳住
掏出冲锋枪打死他
```

类图如下:![](/assets/WeaponStrategy.png)

## 其他

这个是比较常见的方法, 基于Spring 的实现和基于反射的实现, 还有生产上的例子会在习题中给出

