## 目的

1. 尽量减少二个逻辑上相似对象之间转换的代码
2. 提供通用的转换方法

## 例子代码

为什么这个模式怎么没听过呢, 听着很简单呀, 转换对象 get, set 都要写篇文章? ~~这些都是因为小编没时间写太复杂的模式了~~, 这些都是因为小编真的想要写写这个简单的模式.

比如我们想把我们心目中的女神转为我们的女朋友, 这个代码大概如下:

先定义一个女神类:

```java
@Data
public class Goddess {

    private String name;

    private LocalDate birthday;

    private String phoneNumber;
}
```

再定义一个女朋友类:

```java
@Data
public class GirlFriend {

    private String name;

    private Integer age;

    private String phoneNumber;
}
```

这种情况下呢, 我们一般是怎么写的呢, 写一个 Util !!!

```java
public class Goddess2GirlFriendUtil {

    public static GirlFriend convertGoddess2GirlFriend(Goddess goddess) {
        if(goddess == null) {
            throw new RuntimeException("女神不能为空");
        }
        if("兰兰".equals(goddess.getName())) {
            throw new RuntimeException("兰兰不是女神");
        }
        GirlFriend girlFriend = new GirlFriend();
        girlFriend.setName(goddess.getName());
        //根据生日计算年龄
        girlFriend.setAge(LocalDate.now().getYear() - goddess.getBirthday().getYear());
        girlFriend.setPhoneNumber(goddess.getPhoneNumber());
        return girlFriend;
    }

}
```

## 问题分析

其实这种写法并没什么问题, 甚至是最常见的写法, 但是这种写法确实有可能存在二个小问题, 一个是比如我们在女朋友类中需要增加一个邮箱, 这个邮箱我们需要为她注册\(为什么需要给女朋友注册邮箱呀, 这个字段真的牵强\)之后才能返回, 在 Spring 模式下, 有这么个 bean 提供了这样的能力:

```java
public class EmailRegisterBean {

    //假装发生远程调用注册了邮箱并返回
    public String registerForGirlFriend() {
        return "13820802870@163.com";    
    }
}
```

这个时候呢, 由于初始化顺序的关系, 我们发现我们并不能在  static 方法中使用 bean 的这个方法.

另外一个是如果我们需要由批量的把一组女神都转为女朋友\(YOU ARE REALLY TM NB\), 这个时候我们需要在这个类下面定义一个 convertList 方法, 对每个对象执行调用 convertGoddess2GirlFriend 方法来转换, 本来也没什么, 但是如果有100 个, 1000 个这样的工具类都需要批量转换, 那相似的重复代码就很累了

```java
public static List<GirlFriend> convertGoddessList2GirlFriendList(List<Goddess> goddess) {
        return goddess.stream()
                      .map(Goddess2GirlFriendUtil::convertGoddess2GirlFriend)
                      .collect(Collectors.toList());
}
```

## 转换器模式

先定义一个转换器的接口:

```java
public interface Converter<S, T> {

    T transform(S source);

    default List<T> transformList(List<S> sources) {
        return sources.stream().map(this::transform)
                               .collect(Collectors.toList());
    }
}
```

实现从女神到女朋友的转换:

```java
public class Goddess2GirlFriendConverter implements Converter<Goddess, GirlFriend> {

    @Override
    public GirlFriend transform(Goddess goddess) {
        if(goddess == null) {
            throw new RuntimeException("女神不能为空");
        }
        if("兰兰".equals(goddess.getName())) {
            throw new RuntimeException("兰兰不是女神");
        }
        GirlFriend girlFriend = new GirlFriend();
        girlFriend.setName(goddess.getName());
        //根据生日计算年龄
        girlFriend.setAge(LocalDate.now().getYear() - goddess.getBirthday().getYear());
        girlFriend.setPhoneNumber(goddess.getPhoneNumber());
        //此处可以设置 girlFriend 的邮箱了
        return girlFriend;
    }
}
```

最终使用:

```java
public class App {

    public static void main(String[] args) {
        Goddess goddess = new Goddess();
        goddess.setName("翠花");
        goddess.setBirthday(LocalDate.of(2009,1,1));
        goddess.setPhoneNumber("13820802870");
        //生产上是 bean, 单例模式
        System.out.println(new Goddess2GirlFriendConverter().transform(goddess));
        System.out.println(new Goddess2GirlFriendConverter().transformList(Arrays.asList(goddess)));
    }
}
```

输出结果:

```java
GirlFriend(name=翠花, age=10, phoneNumber=13820802870, email=null)
[GirlFriend(name=翠花, age=10, phoneNumber=13820802870, email=null)]
```

## 生产例子

转换模式既可以用作分层框架下各个层次之间相似甚至相同对象的转换, 又可以处理一些简单的需要查询外部信息等的赋值逻辑

其实我们基于分层架构上来讲, 最好的实践应该是每层的对象之间隔离, 每层即使是相同的对象可能也需要定义不同的对象来实现层次之间的隔离.比如 dao 层应该有 dao 层自己的对象, service 层应该有自己的对象, facade 和 controller 层也需要自己的对象, 如果有更细的分层, 比如 SOFA 框架, 也需要依次类推.

其实一开始我觉得是没必要的, 包括我之前在生产上使用什么 VO, DTO, DO 什么的都是随心所欲的乱用, 之前我在福报厂做对客系统, 调用别人的接口获取贷款信息, 我把他们的 Production 类从 client 层传到了 controller 层, 并没有进行层与层之间的分类, 后来他们的接口升级啦, 我接到需求心态崩了, 好几万行代码在使用这个老的类, 让我逐一替换?? 后来我机智的解决了, 但是也埋了很多坑, 所以说转换模式在这种分层情况下就可以起到很大的作用, 是个简单但重要的模式



