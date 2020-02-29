## 目的

将现有的代码能力适配到新的接口

## 例子代码

赵高欲为乱, 恐群臣不听, 乃先设验, 持鹿献于二世日: "马也" 二世笑曰: "丞相误耶? 谓鹿为马." 问左右, 左右或默, 或言马以阿顺赵高, 或言鹿者. 高因阴中诸言鹿者以法

这就是很有名的指鹿为马的故事了, 我们可以看到赵高需要的是个马, 但实际上是个鹿, 我们怎么通过适配器模式来实现他的指鹿为马呢

我们先定义一下鹿和马的接口:

马:

```java
public interface Horse extends Target {

    void eat();

    void run();

    void sleep();
}
```

鹿:

```java
public interface Deer extends Adaptee {

    void eat();

    void walk();

    void sleep();
}
```

其中 Target 和 Adaptee 是标记形接口, 无实际意义

本来这二个接口其实没什么关联, 但是现在的情况是我们有个鹿, 但是要给皇帝一匹马, 先看看我们这条鹿:

```java
public class MyDeer implements Deer {
    @Override
    public void eat() {
        System.out.println("吃草");
    }

    @Override
    public void walk() {
        System.out.println("走路, 遇到紧急情况冲刺");
    }

    @Override
    public void sleep() {
        System.out.println("睡觉");
    }
}
```

我们将这个鹿变成马:

```java
@Data
@AllArgsConstructor
public class HourseAdapter implements Horse {

    private Deer deer;

    @Override
    public void eat() {
        deer.eat();
    }

    @Override
    public void run() {
        deer.walk();
    }

    @Override
    public void sleep() {
        deer.sleep();
    }
}
```

使用的时候就是这样:

```java
Horse horse = new HourseAdapter(new MyDeer());
horse.eat();
horse.run();
horse.sleep();
```

输出结果:

```
吃草
走路, 遇到紧急情况冲刺
睡觉
```

类图:

### ![](/assets/2020022900.png)

一般微服务结构下或者依赖第三方接口或者 jar 包的时候, 可以考虑使用适配器模式对代码进行兼容和模型防腐













