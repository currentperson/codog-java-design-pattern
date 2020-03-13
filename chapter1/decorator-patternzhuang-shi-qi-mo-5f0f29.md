## 目的

在不改变原有代码的前提下为原来的方法增加新的能力

## 例子代码

我今天在朋友圈突然发现了大家会分享自己的 "剪映" 的照片, 不出所料, 又是字节跳动的 APP, 只要是娱乐方向 APP, 只有你想不到, 没有字节跳动没做的, 8 周年一鸣大佬又要提高员工的幸福度又要进军教育了, "让天下没有难办的教育" 马上就要成为目标了...

我大概看了看视频, 剪映大概是个图片渲染还是个画图软件类似的, 今天很火的好像是个 "手拿花花", 大概是这个样子:

![](/assets/2020031200.png)

所以呢, 这种类似于图片的修剪, 确实是装饰器模式最好的例子, 我们可以先假设普通的方法来实先这个功能, 那么大概是这样:

```
//用户先放了一个手
Hand hand = new Hand();
//用户创建了绿叶
HandWithLeaf handWithLeaf = new HandWithLeaf();
handWithLeaf.setHand(hand);
//用户创建完绿叶又创建了花
HandWithLeafAndFlower = new HandWithLeafAndFlower();
............
```

## 问题分析

上面代码的问题显而易见, 一个是类太多了, HandWithLeafAndFlower 是个类, 如果用户先放花后放绿叶 那HandWithFlowerAndFlower 又是一个类了

## 装饰器模式

我们先定义一个接口来代表展示图片:

```java
public interface Component {
    String showPicture();
}
```

再来定义一个基础类, 就是手:

```java
public class Hand implements Component {

    @Override
    public String showPicture() {
        return "展示纤纤玉手";
    }
}
```

定义一个装饰器的基类:

```java
public abstract class Decorator implements Component {
        protected Component component;

    public Decorator(Component component) {
        this.component = component;
    }
}
```

定义几个装饰器:

花装饰器:

```java
public class FlowDecorator extends Decorator {

    public FlowDecorator(Component component) {
        super(component);
    }

    @Override
    public String showPicture() {
        return component.showPicture() + "加上鲜花";
    }
}
```

叶子装饰器:

```java
public class LeafDecorator extends Decorator {

    public LeafDecorator(Component component) {
        super(component);
    }

    @Override
    public String showPicture() {
        return component.showPicture() + "加上绿色叶子";
    }
}

```

音乐装饰器:

```java
public class MusicDecorator extends Decorator {

    public MusicDecorator(Component component) {
        super(component);
    }

    @Override
    public String showPicture() {
        return component.showPicture() + "加上播放音乐";
    }
}
```

然后看下效果:

```java
public class App {
    public static void main(String[] args) {
        Component hand = new Hand();
        System.out.println(new MusicDecorator(new LeafDecorator(new FlowDecorator(hand))).showPicture());
    }
}
```

输出:

```java
展示纤纤玉手加上鲜花加上绿色叶子加上播放音乐
```

类图:![](/assets/2020031301.png)

## 课后作业

1. 如果我们需要定义一个银行首页, 本来的对象叫做 indexVo, 如果是新手要在右上方展示新手图片, 如果是逾期用户要把背景色变红, 如果是 VIP 用户要播放音乐, 请使用装饰器模式实现
2. 有几种优惠卷, 一种是打 5 折, 一种是减免 20 元, 一种是消费满 40 减 5 元, 满 100 减 15 元, 这三种优惠卷的优先级就是如题目所述, 用户可能都勾选, 但是可能只能用到其中一二张, 给出用户总的消费金额, 返回用户真实要支付的金额





