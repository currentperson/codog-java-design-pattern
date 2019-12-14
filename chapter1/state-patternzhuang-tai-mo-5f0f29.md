## 目的

将各个状态在不同外在事件影响下的表现清晰的呈现出来, 添加新的状态的时候尽量少改动代码

## 例子代码

最近的北大女孩自杀事件让 PUA 进入了大家的视野, 我个人趁此看了下 PUA, 觉得没啥用, 真的想找到另一半最后不还是要~~真心~~ \(钱\)么, 为了避免更多的女孩陷入 PUA 陷阱, 我们在这里使用状态模式讲讲 PUA 的那些事:

下面是一个女孩的状态:

```java
@Getter
@AllArgsConstructor
public enum GetLongStateEnum {
    NOT_KNOWN_EACH_OTHER("NOT_KNOWN_EACH_OTHER", "不认识"),
    NEED_ADD_WE_CHAT("NEED_ADD_WE_CHAT", "加了微信"),
    WAIT_DATE("WAIT_DATE","等约会"),
    FEEL_SORRY("FEEL_SORRY", "心生怜悯"),
    MAKE_LOVE("MAKE_LOVE", "学习英语");

    private String code;

    private String desc;

}
```

下面我们定义一个被 PUA 讨论的女孩类:

```java
public class PUABadGirl {

    private GetLongStateEnum state = NOT_KNOWN_EACH_OTHER;

    //搭讪
    public void accosted(String talkMessage) {
        if (state == NOT_KNOWN_EACH_OTHER) {
            if ("你看起来很高冷, 其实认识了就话很多".equals(talkMessage)) {
                System.out.println("你懂我, 加个微信吧");
                state = NEED_ADD_WE_CHAT;
            } else {
                System.out.println("微信就是我手机号: 110");
            }
            return;
        }

        if (state == MAKE_LOVE) {
            System.out.println("都学完英语了还搭讪, 肾虚影响记忆力了吧");
            return;
        }
    }

    //加微信
    public void addWeChat(String moments) {
        if (state == NOT_KNOWN_EACH_OTHER) {
            System.out.println("都不认识上来就加微信, 不加, '学英语' 还得前戏呢");
            return;
        }

        if (state == NEED_ADD_WE_CHAT) {
            if ("朋友圈前十条都是跑车, 豪宅, 巴黎铁搭, 伦敦大本钟".equals(moments)) {
                System.out.println("说不定家里还有 拆 字, 等约会吧");
                state = WAIT_DATE;
            } else {
                System.out.println("全是沙雕表情包, 还都是周日发的, 一看就是屌丝上班狗, 不加了");
            }
            return;
        }

        if (state == FEEL_SORRY) {
            System.out.println("昨天还是你和我说亲戚死了要去参加, 今天咋就在这加微信");
            return;
        }

        if (state == MAKE_LOVE) {
            System.out.println("都学完英语了才加微信, 要给我微信转钱么");
            return;
        }
    }

    //约会
    public void date(String talkMessage) {
        if (state == NOT_KNOWN_EACH_OTHER) {
            System.out.println("都不认识上来就要约会, 叔叔我们不约不约");
            return;
        }

        if (state == NEED_ADD_WE_CHAT) {
            System.out.println("没加微信约会没话题聊, 不约不约");
            return;
        }

        if (state == WAIT_DATE) {
            if ("我们亲戚最近去世了, 我是他养大的".equals(talkMessage)) {
                state = FEEL_SORRY;
            } else {
                System.out.println("连美国好声音的选手都知道唱歌完比惨, 你不惨不高不帅也没有钱这个肯定单身");
            }
            return;
        }

        if (state == MAKE_LOVE) {
            System.out.println("都学完英语了还约会, 这不是 PUA, 这是暖男");
        }
    }

    //学英语
    public void makeLove() {
        if (state == NOT_KNOWN_EACH_OTHER) {
            System.out.println("不认识就要学英语!!!");
            return;
        }

        if (state == FEEL_SORRY) {
            System.out.println("他好可怜, 我要和他生猴子");
            state = MAKE_LOVE;
            return;
        }

        if (state == MAKE_LOVE) {
            System.out.println("学完英语还要学, 你真是个好学的好伙子");
            return;
        }
    }
}
```

下面就是套路了:

```java
    public static void main(String[] args) {
        PUABadGirl puaBadGirl = new PUABadGirl();
        puaBadGirl.accosted("你看起来很高冷, 其实认识了就话很多");
        puaBadGirl.addWeChat("朋友圈前十条都是跑车, 豪宅, 巴黎铁搭, 伦敦大本钟");
        puaBadGirl.date("我们亲戚最近去世了, 我是他养大的");
        puaBadGirl.makeLove();
    }
```

## 问题分析

这个对状态之间的扭转还使用卫语句, 可读性仍然非常差, 需要把不同的状态接收了不同是操作的不同的表现清晰的看出来就很难了, 如果要知道 FEEL\_SORRY 状态女孩对搭讪, 加微信, 约会的反应, 10 秒内一般人是看不出来了, 另外就是一旦加了一种状态, 咱们比如说增加了女生心情不好的状态, 需要这种状态的女孩, 在搭讪,  加微信, 约会进行不同的反应, 那么 if-else加个没完了

## 状态模式

同样是被 PUA 的女孩, 她的表现可以根据状态的不同而完全不同, 所以我们把PUA女孩变成如下类:

```java
@Data
public class PUAGirl {

    private AbstractPUAGirlState abstractPUAGirlState = new NotKnownEachOtherState(this);

    //搭讪
    public void accosted(String message) {
        abstractPUAGirlState.accosted(message);
    }

    //加微信
    public void addWeChat(String message) {
        abstractPUAGirlState.addWeChat(message);
    }

    //约会
    public void date(String message) {
        abstractPUAGirlState.date(message);
    }

    //学英语
    public void makeLove() {
        abstractPUAGirlState.makeLove();
    }
}
```

抽象状态如下:

```java
public abstract class AbstractPUAGirlState {

    protected PUAGirl puaGirl;

    AbstractPUAGirlState(PUAGirl puaGirl) {
        this.puaGirl = puaGirl;
    }

    //搭讪
    public abstract void accosted(String talkMessage);

    //加微信
    public abstract void addWeChat(String moments);

    //约会
    public abstract void date(String talkMessage);

    //学英语
    public abstract void makeLove();
}
```

我们定义如下的状态实现类:

互相不认识:

```java
public class NotKnownEachOtherState extends AbstractPUAGirlState {

    public NotKnownEachOtherState(PUAGirl puaGirl) {
        super(puaGirl);
    }

    @Override
    public void accosted(String talkMessage) {
        if ("你看起来很高冷, 其实认识了就话很多".equals(talkMessage)) {
            System.out.println("你懂我, 加个微信吧");
            this.puaGirl.setAbstractPUAGirlState(new NeedAddWeChatState(this.puaGirl));
        } else {
            System.out.println("微信就是我手机号: 110");
        }
    }

    @Override
    public void addWeChat(String moments) {
        System.out.println("都不认识上来就加微信, 不加, '学英语' 还得前戏呢");
    }

    @Override
    public void date(String talkMessage) {
        System.out.println("都不认识上来就要约会, 叔叔我们不约不约");
    }

    @Override
    public void makeLove() {
        System.out.println("不认识就要学英语!!!");
    }
}
```

需要加微信:

```java
public class NeedAddWeChatState extends AbstractPUAGirlState {

    public NeedAddWeChatState(PUAGirl puaGirl) {
        super(puaGirl);
    }

    @Override
    public void accosted(String talkMessage) {

    }

    @Override
    public void addWeChat(String moments) {
        if ("朋友圈前十条都是跑车, 豪宅, 巴黎铁搭, 伦敦大本钟".equals(moments)) {
            System.out.println("说不定家里还有 拆 字, 等约会吧");
            this.puaGirl.setAbstractPUAGirlState(new WaitDateState(this.puaGirl));
        } else {
            System.out.println("全是沙雕表情包, 还都是周日发的, 一看就是屌丝上班狗, 不加了");
        }
    }

    @Override
    public void date(String talkMessage) {
        System.out.println("没加微信约会没话题聊, 不约不约");
    }

    @Override
    public void makeLove() {

    }
}
```

等待约会:

```java
public class WaitDateState extends AbstractPUAGirlState {

    public WaitDateState(PUAGirl puaGirl) {
        super(puaGirl);
    }

    @Override
    public void accosted(String talkMessage) {

    }

    @Override
    public void addWeChat(String moments) {

    }

    @Override
    public void date(String talkMessage) {
        if ("我们亲戚最近去世了, 我是他养大的".equals(talkMessage)) {
            this.puaGirl.setAbstractPUAGirlState(new FeelSorryState(this.puaGirl));
        } else {
            System.out.println("连美国好声音的选手都知道唱歌完比惨, 你不惨不高不帅也没有钱这个肯定单身");
        }
    }

    @Override
    public void makeLove() {

    }
}
```

感到抱歉:

```java
public class FeelSorryState extends AbstractPUAGirlState {

    public FeelSorryState(PUAGirl puaGirl) {
        super(puaGirl);
    }

    @Override
    public void accosted(String talkMessage) {

    }

    @Override
    public void addWeChat(String moments) {
        System.out.println("昨天还是你和我说亲戚死了要去参加, 今天咋就在这加微信");
    }

    @Override
    public void date(String talkMessage) {

    }

    @Override
    public void makeLove() {
        System.out.println("他好可怜, 我要和他生猴子");
    }
}
```

使用方法和调用方法还是如上.

类图:![](/assets/2019121401.png)

## 课后作业

一 如果每个状态都有百分之五十的状态是原来的状态_, _百分之五十的状态是 NOTHAPPY, 在  NOT\_HAPPY 这中状态下, 搭讪的行为是扇巴掌, 加微信的行为是打 110, 约会的行为是放鸽子, 学英语的行为是...., 请分别使用非状态模式和状态模式实现

二 学习 Spring StateMachine 框架

三 请把文中的例子用数据存储状态

四 银行的账户有不同的状态, 账户操作分为存款, 取款, 注销等操作, 根据账号中的余额不同进行不行操作的反应

