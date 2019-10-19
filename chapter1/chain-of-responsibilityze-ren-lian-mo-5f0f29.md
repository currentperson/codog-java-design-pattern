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

先定义一套责任链的共有类:

```java
public interface ChainNode<T extends AbstractChainNodeResponse> {

    <C extends AbstractChainNodeContext, R extends AbstractChainNodeRequest> ChainResult<T> execute(C context, R request);
}
```

其中的类都是空实现, chainResult 如下:

```java
@Data
public class ChainResult<T extends AbstractChainNodeResponse> {

    private boolean processingCompleted;

    private T response;
}
```

定义一个责任链处理器:

```java
public class ChainProcessor {
    public static <T extends AbstractChainNodeResponse>
        T handleChainNodes(List<ChainNode<T>> chainNodes,
                       AbstractChainNodeContext context,
                       AbstractChainNodeRequest request, T defaultValue) {
        for (ChainNode<T> chainNode : chainNodes) {
            ChainResult<T> execute = chainNode.execute(context, request);
            if (execute.isProcessingCompleted()) {
                return execute.getResponse();
            }
        }
        return defaultValue;
    }
}
```

然后我们结合例子代码进行使用:

自定义返回值:

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class FatherNameChainNodeResponse extends AbstractChainNodeResponse {

    //父亲姓名
    private String fatherName;

}
```

使得大头儿子继承入参类:

```java
@Getter
public class BigHeadSon extends AbstractChainNodeRequest {

    //是否尖鼻子
    private final boolean pointedNose = false;

    //是否粗眉毛
    private final boolean coarseEyebrows = false;

    //是否大头
    private final boolean bigHead = true;
}
```

实现一个抽象的找爸爸类:

```java
public abstract class CheckFatherChainNode implements ChainNode<FatherNameChainNodeResponse> {

    @Override
    public ChainResult<FatherNameChainNodeResponse> execute(AbstractChainNodeContext context, AbstractChainNodeRequest request) {
        return null;
    }
}
```

尖鼻子检查:

```java
public class NoseCheckFatherChainNode extends CheckFatherChainNode {
    @Override
    public ChainResult<FatherNameChainNodeResponse> execute(AbstractChainNodeContext context, AbstractChainNodeRequest request) {
        BigHeadSon bigHeadSon = (BigHeadSon) request;
        ChainResult<FatherNameChainNodeResponse> chainResult = new ChainResult<>();
        if (bigHeadSon.isPointedNose()) {
            chainResult.setProcessingCompleted(true);
            chainResult.setResponse(new FatherNameChainNodeResponse("尖鼻子厨师"));
        }
        return chainResult;
    }
}
```

粗眉毛检查:

```java
public class EyeBrowCheckFatherChainNode extends CheckFatherChainNode {
    @Override
    public ChainResult<FatherNameChainNodeResponse> execute(AbstractChainNodeContext context, AbstractChainNodeRequest request) {
        BigHeadSon bigHeadSon = (BigHeadSon) request;
        ChainResult<FatherNameChainNodeResponse> chainResult = new ChainResult<>();
        if (bigHeadSon.isCoarseEyebrows()) {
            chainResult.setProcessingCompleted(true);
            chainResult.setResponse(new FatherNameChainNodeResponse("粗眉毛保安"));
        }
        return chainResult;
    }
}
```

大头检查:

```java
public class HeadCheckFatherChainNode extends CheckFatherChainNode {
    @Override
    public ChainResult<FatherNameChainNodeResponse> execute(AbstractChainNodeContext context, AbstractChainNodeRequest request) {
        BigHeadSon bigHeadSon = (BigHeadSon) request;
        ChainResult<FatherNameChainNodeResponse> chainResult = new ChainResult<>();
        if (bigHeadSon.isBigHead()) {
            chainResult.setProcessingCompleted(true);
            chainResult.setResponse(new FatherNameChainNodeResponse("隔壁老王"));
        }
        return chainResult;
    }
}
```

最终使用:

```java
public class App {
    public static void main(String[] args) {
        System.out.println(ChainProcessor.handleChainNodes(Arrays.asList(new NoseCheckFatherChainNode(),
                new EyeBrowCheckFatherChainNode(), new HeadCheckFatherChainNode()),
                null, new BigHeadSon(), new FatherNameChainNodeResponse("小头爸爸")));
    }
}
```

一顿操作猛如虎, 一看还是带绿帽

