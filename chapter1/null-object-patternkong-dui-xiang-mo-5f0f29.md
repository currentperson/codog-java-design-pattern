## 目的

避免上层代码进行空指针异常检查和保持上层代码的可读性和简洁性

## 例子代码

今年你妈逼你结婚了么? 程序猿的单身, 每到情人节, 七夕节, 1024 程序猿节甚至本来是单身狗保护日的双十一, 我们都被无情的秀恩爱秀了一脸, 最可怕的是到了过年回家, 面对七大姑八大姨的质问, 一句大侄子处对象了么, 一句还没呢足以让你成为接下来半小时的重点照顾对象\(是不是眼光高呀, 我们村有个叫兰兰的不错, 你是不是 gay 呀\), 对于这种情况我只有以下三点要说: ...

父母这面更是花式催婚啦, 什么看奶粉广告\(你看电视这大胖小子, 多可爱\), 什么暗语\(你前面有 10 条巷子, 9 条有危险, 你要怎么走过去\)

那我们需要假装自己有对象, 这样大家就不会对你抛出空指针异常警告, 可是我们是老老实实的程序猿, 并不会编造一个女朋友\(那这就是 mock 啦\). 我们只是根据实际情况作答

我们需要一个女朋友的定义, 大致如下:

```java
/**
 * 女朋友属性
 */
public abstract class AbstractGirlFriend {

    /**
     * 查询姓名
     * @return
     */
    protected abstract String queryName();

    /**
     * 查询年龄
     * @return
     */
    protected abstract int queryAge();

    /**
     * 查询 cup
     * @return
     */
    protected abstract Character queryCup();

    /**
     * 是不是女性
     * @return
     */
    protected abstract boolean isFemale();
}
```

我们定义一个抽象男人类, 返回女朋友类.

```java
/**
 * 男人
 */
public abstract class Man {

    /**
     * 得到女朋友
     * @return
     */
    public abstract AbstractGirlFriend queryGirlFriend();
}
```

定义了二个实现类:

别人:

```java
/**
 * 其他人
 */
public class Others extends Man {

    @Override
    public AbstractGirlFriend queryGirlFriend() {
        return new NormalGrilFriend();
    }
}
```

我自己:

```java
/**
 * 我自己
 */
public class Me extends Man {

    @Override
    public AbstractGirlFriend queryGirlFriend() {
        return null;
    }
}
```

其中 NormalGrilFriend 大致如下:

```java
/**
 * 正常的女朋友
 */
public class NormalGrilFriend extends AbstractGirlFriend implements PlayWithGirlFriend {

    @Override
    protected String queryName() {
        return "兰兰";
    }

    @Override
    protected int queryAge() {
        return 18;
    }

    @Override
    protected Character queryCup() {
        //嗯, 经过我阅片 20 年的经验, 这个类也是 A, 你没有看错
        return 'A';
    }

    @Override
    protected boolean isFemale() {
        return true;
    }

    @Override
    public void date() {
        System.out.println("吃饭, 看电影, 游乐园, 小树林");
    }

    @Override
    public void shopping() {
        System.out.println("买买买, 刷刷刷");
    }

    @Override
    public void mkLove() {
        System.out.println("嗯, 这个方法我也不知道要干啥, 请女读者(男的也行)联系我现场教学, 微信是 13820802870");
    }
}
```

这时候我们有个需求, 需要打印一组男人所有女朋友的名字, 我们的代码大致如下:

```java
    List<Man> mansWithGrilFriend = Arrays.asList(new Others(), new Me());

    for (Man man : mansWithGrilFriend) {
        AbstractGirlFriend girlFriend = man.queryGirlFriend();
        if(girlFriend == null) {
            continue;
        }
        System.out.println(girlFriend.queryName());
    }
```

## 问题分析

我们觉得有二点问题, 第一点是我们需要做 girlFriend == null 这种判断, 别人阅读代码时候本来只要看输出姓名这一行就知道这个  for 循环做了什么, 现在还需要多看二行, 另外是如果不是这个严谨的上层使用者, 没有做非空判断, 随时可能报出空指针异常.

## 空对象模式

我们定义一个空的女朋友对象:

```java
/**
 * 空女朋友
 */
public final class NullGrilFriend extends AbstractGirlFriend implements PlayWithGirlFriend {

    private static final NullGrilFriend NULL_GRIL_FRIEND = new NullGrilFriend();

    private NullGrilFriend() {
    }

    public static NullGrilFriend getInstance() {
        return NULL_GRIL_FRIEND;
    }

    @Override
    protected String queryName() {
        return "";
        //return "五指姑娘";
    }

    @Override
    protected int queryAge() {
        //没出生呢
        return 0;
    }

    @Override
    protected Character queryCup() {
        // A 也找不着呀, 所以不能乱说, 需要减 1
        return 'A' - 1;
    }

    @Override
    protected boolean isFemale() {
        //这个是 IDEA 自动生成的代码
        return false;
    }

    @Override
    public void date() {
        System.out.println("打开电脑 D 盘下面的 '学习视频' 目录下的波老师教学");
    }

    @Override
    public void shopping() {
        System.out.println("买些女装自己穿, 买到让淘宝广告以为我是暖男");
    }

    @Override
    public void mkLove() {
        System.out.println("哎, 手上的茧");
    }
}
```

在我自己的类中返回这个空的女朋友:

```
/**
 * 我自己
 */
public class Me extends Man {

    @Override
    public AbstractGirlFriend queryGirlFriend() {
        return NullGrilFriend.getInstance();
    }
}
```

这样我们就是去掉空指针判断了, 代码的就可以像下面这样可读性变的好了很多

```
    List<Man> mansWithGrilFriend = Arrays.asList(new Others(), new Me());
    for (Man man : mansWithGrilFriend) {
        System.out.println(man.queryGirlFriend().queryName());
    }
```



类图如下:

![](/assets/AbstractGirlFriend.png)

Tips: 还可以使用 Nullable 接口和是 Null 标记接口来提高代码的可读性, 在实现空对象模式的时候需要注意保持上层代码的行为要可控

