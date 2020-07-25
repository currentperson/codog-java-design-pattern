## 目的

做新老代码迁移的时候保证原有功能逐步迁移并且不会印象开发新功能

## 例子代码

最近一杭州女子离奇失踪的事情引起了大家的关注, 大致是讲一位女士被报案失踪, 小区的监控全部没监视到如何离开小区的, 目前的结果竟然是重组家庭的丈夫因为利益原因将其杀害分尸并抛到化粪池, 之后还淡定报警接受采访等, 小道消息说他动手之前还问了问老婆那句 "我还有机会么",,,,,

![](/assets/2020072500.png)

画龙画虎难画骨, 知人知面不知心, 本来是重组家庭, 女子类似于把一个旧的丈夫替换成了新人, 没有经过更好的过渡, 就好比我们要重构一个复杂系统中某个类, 里面的方法都写完了再替换可能会造成时间和风险上的不确定性增多, 我们最好是相似的接口一个一个的替换.逐步上线

## 绞杀者模式

定义一个丈夫的接口:

```java
public interface Man {

    void eat();

    void drink();

    void sleep();
}
```

老的实现类:

```java
public class OldMan implements Man {
    @Override
    public void eat() {
        System.out.println("爱吃狗不理包子");
    }

    @Override
    public void drink() {
        System.out.println("爱喝白酒");
    }

    @Override
    public void sleep() {
        System.out.println("8 点睡到 8 点");
    }
}
```

新的实现类:

```java
public class NewMan implements Man {
    @Override
    public void eat() {
        System.out.println("爱吃西湖醋鱼");
    }

    @Override
    public void drink() {
        System.out.println("爱喝啤酒");
    }

    @Override
    public void sleep() {
        throw new 
            RuntimeException("还没实现, 请调用老接口");
    }
}
```

过渡类:

```java
public class HalfMan implements Man {

    private Man oldMan;

    private NewMan newMan;

    public HalfMan(Man oldMan, NewMan newMan) {
        this.oldMan = oldMan;
        this.newMan = newMan;
    }

    @Override
    public void eat() {
        newMan.eat();
    }

    @Override
    public void drink() {
        newMan.drink();
    }

    @Override
    public void sleep() {
        oldMan.sleep();
    }
}
```

使用方式:

```java
public static void main(String[] args) {
    Man man = new HalfMan(new OldMan(),new NewMan());
    man.sleep();
}
```

输出:

```
8 点睡到 8 点
```

类图:

![](/assets/2020072501.png)

