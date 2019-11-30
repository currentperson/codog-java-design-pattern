2. 目的

使得客户端不必要关心产品的构造的细节

## 例子代码

最近也不知道玩什么, 开始想想有什么动漫可以追, 以下是我追过的一些动漫了, 小伙伴们有什么喜欢的动漫么?

![](/assets/2019113001.png)

![](/assets/2019113002.png)

![](/assets/2019113004.png)

现在我们要做这个一个功能, 给出一个动漫的名字, 给出主角的名字和主要技能, 我们可能会这么写:

```java
public static Protagonist createProtagonist(String comicType) {
    if (Objects.equals(comicType, "火影忍者")) {
        return new Protagonist("鸣人", "九尾 + 嘴遁");
    }
    if (Objects.equals(comicType, "进击的巨人")) {
        return new Protagonist("艾伦", "巨人之力 + 三笠");
    }
    if (Objects.equals(comicType, "一拳超人")) {
        return new Protagonist("琦玉", "认真一拳 + 被 King 召唤术");
    }
    return Protagonist.NULL;
}
```

~~这就是简单工厂模式了, 完事了~~

## 问题分析

很明显, 这么写会造成 if - else 过多, 不好阅读, 并且不好维护, ~~也没用泛型, 怎么显示出技术呢~~

## 简单工厂模式

定义简单工厂的入参:

```java
@Data
public class SampleFactoryParam<E extends Enum<E>> {
    private Enum<E> type;
}
```

定义产品的基类:

```java
@Data
@AllArgsConstructor
public class FactoryProductionResult<T> {

    public static final FactoryProductionResult NULL = 
        new FactoryProductionResult<>(Boolean.FALSE, null);
    private Boolean isExisted;
    private T production;
}
```

定义简单工厂的工具类:

```java
public class SampleFacoryUtil {

    public static final <E extends Enum<E>, T>
        FactoryProductionResult<T> 
        createProdction(E type, Map<E, T> type2Production) {
            T production = type2Production.get(type);
            return production == null ? FactoryProductionResult.NULL 
            : new FactoryProductionResult<>(Boolean.TRUE, production);
    }
}
```

实际使用:

```java
public static void main(String[] args) {
    System.out.println(BadProtagonistFactory.createProtagonist("一拳超人"));
    System.out.println(SampleFacoryUtil.
        createProdction(NameEnum.JIN_JI_DE_JU_REN, 
            Constant.NAME_2_PROTAGONIST_MAP));
}
```

## 课后作业

1. 给定一个 Range 类, 代表借款金额范围在此范围, 返回不同的折扣力度

   比如低于 100不打折, 100-200 返回 8 折, 200-300 返回 7 折..

   1. 

完善文中例子代码 m中获取不到, 提供默认值选项

