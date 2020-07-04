## 目的

限制类的实例数量在系统内唯一

## 例子代码

最近 &lt;隐秘的角落&gt; 作为一个口碑流量俱佳的网剧, 我给大家讲一讲, 大概情节是这样的:

里面一个脱发程序员, 带着老婆的爸妈去登山拍照, 没想到秀了个发型把人家都吓到山下摔死了, 后续被三个~~音~~\(阴\)乐爱好者边唱着&lt;小白船&gt; \([https://y.qq.com/portal/player.html](https://y.qq.com/portal/player.html)\) 边给录了下视频, 和他讲是这事老婆知道了肯定得闹离婚, 最好给他们三十万, 他们就把内存卡还给他, 结果复制了个内存卡欺负我们老实程序员, 老实人就不开心了, 开始了一局五杀的路程, 其实这个故事告诉我们~~每个人都会有隐秘的角落~~, \(内存卡要是用单例模式就不死人了\)

使用代码大概是这样描述:

先定义一个内存卡:

```java
//内存卡
@Data
public class MemoryCard {

    private Integer size = 1024;

    private String content = "资深程序员吓倒丈母娘.MP4";
}
```

再定义一个想要内存卡的老实人:

```java
//老实程序员
@Data
public class SimpleCoder {
    private MemoryCard memoryCard;
}
```

再定义一个手持证据的音乐爱好者:

```java
//音乐爱好者
@Data
public class MusicLover {
    private String name;
    private MemoryCard memoryCard;

    public MemoryCard getMemoryCardCopy() {
        return BeanUtil.deepClone(memoryCard, MemoryCard.class);
    }
}
```

我们来简单描述一下这场交易:

```java
MusicLover musicLover = new MusicLover();
musicLover.setName("张朝阳");
musicLover.setMemoryCard(new MemoryCard());
SimpleCoder simpleCoder = new SimpleCoder();
simpleCoder.setMemoryCard(musicLover.getMemoryCardCopy());
//销毁内存卡
simpleCoder.setMemoryCard(null);
System.out.println("老实人: " + simpleCoder.toString());
System.out.println("音乐爱好者: " + musicLover.toString());
```

输出:

```java
老实人: SimpleCoder(memoryCard=null)
音乐爱好者: MusicLover(name=张朝阳, memoryCard=MemoryCard(size=1024, content=资深程序员吓倒丈母娘.MP4))
```

## 问题分析

如果内存卡是单例模式也就不会造成老实人被骗, 兔子急了咬人的场景了

## 单例模式

我们把内存卡变成单例的:

```java
@ToString
public enum MemoryCard {

    INSTANCE(1024,"资深程序员吓倒丈母娘.MP4");

    private Integer size;

    private String content;

    MemoryCard(Integer size, String content) {
        this.size = size;
        this.content = content;
    }

    public void setNull() {
        this.content = "";
    }
}
```

我们再来描述这种交易:

```java
MusicLover musicLover = new MusicLover();
musicLover.setName("张朝阳");
musicLover.setMemoryCard(MemoryCard.INSTANCE);
SimpleCoder simpleCoder = new SimpleCoder();
simpleCoder.setMemoryCard(musicLover.getMemoryCardCopy());
//销毁内存卡
simpleCoder.getMemoryCard().setNull();
System.out.println("老实人: " + simpleCoder.toString());
System.out.println("音乐爱好者: " + musicLover.toString());
```

输出:

```java
老实人: SimpleCoder(memoryCard=MemoryCard.INSTANCE(size=1024, content=))
音乐爱好者: MusicLover(name=张朝阳, memoryCard=MemoryCard.INSTANCE(size=1024, content=))
```

类图:

![](/assets/2020070400.png)

