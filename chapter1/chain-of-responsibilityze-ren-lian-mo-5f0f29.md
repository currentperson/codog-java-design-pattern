## 目的

在发送请求的类和最终处理的类之间进行解耦

## 例子代码

小伙伴们都看过 [大头儿子小头爸爸](http://v.qq.com/detail/p/p8523is4ct895z7.html?ptag=baidu_aladdin.cartoon) 么\(大手牵小手, 走路不怕滑~\), 当初纯洁的我怎么也没想到小头爸爸的绿帽子这么鲜艳

![](/assets/2019101901.png)

话说大家都这么说, 大头妈妈天天在家里生气, 小头爸爸内心也忍不住了, 于是要证明自己的清\(bei\)白\(lv\)

![](/assets/2019101902.png)

除了王叔叔, 还有二个他很怀疑的对象,  尖鼻子厨师和粗眉毛保安大哥, 他现在就想看看这到底是谁的责任, 于是我们可能会这样写:

![](/assets/2019101904.png)

![](/assets/2019101905.png)

这个时候他采取了如下的代码:

定义一个大头儿子类:

```java
@Getter
public class BigHeadSon {

    //是否尖鼻子
    private final boolean pointedNose = false;

    //是否粗眉毛
    private final boolean coarseEyebrows = false;

    //是否大头
    private final boolean bigHead = true;
}
```

定义一个决定儿子爸爸的类:

```java
public class DecideFather {

    public static String findFatherName(BigHeadSon son) {
        if (son.isPointedNose()) {
            return "尖鼻子厨师";
        }
        if (son.isCoarseEyebrows()) {
            return "粗眉毛保安";
        }
        if (son.isBigHead()) {
            return "隔壁老王";
        }
        return "小头爸爸";
    }
}
```

然后小头爸爸试了试:

```java
public class App {

    public static void main(String[] args) {
        System.out.println(DecideFather.findFatherName(new BigHeadSon()));
    }
}
```

## 问题分析

很明显, if-else 可能无限的地方就是设计模式可以使用的地方, 这时候比如他又怀疑了卖狗狗的大胡子叔叔, 那他又要加个 if-else 了, 这个函数最终可能上百行, 变量可能共享, 那他就很难受了

## 责任链模式





