## 目的

将二个变化的维度分离开, 简而言之就是双重策略模式模式

## 例子代码

在中国古代神话中, 有很多仙女下凡与凡人相恋的故事, 比如牛郎-织女, 董永-七仙女, 刘彦昌-三圣母等等, 他们之间都隔着一道银河, 我们假设牛郎想去七仙女和三圣母那串门, 这个牛郎又看过\([策略模式](/chapter1/strategy-patternce-lve-mo-5f0f29.md)\), 他想到他以后可能还要访问别的仙女\(这到底是不是我认识的牛郎,,,\), 就策略模式又搭了二个鹊桥\(泛型方式请看策略模式文章\):

```java
public interface NiuLangVisitStrategy {
    String visitGirl();
}
```

牛郎访问织女:

```java
public class NiuLangVisitZhiNvStrategyImpl implements NiuLangVisitStrategy {
    @Override
    public String visitGirl() {
        return "牛郎访问了织女";
    }
}
```

牛郎访问三圣母:

```java
public class NiuLangVisitSanShengMuStrategyImpl implements NiuLangVisitStrategy {
    @Override
    public String visitGirl() {
        return "牛郎访问了三圣母";
    }
}
```

牛郎访问七仙女:

```java
public class NiuLangVisitQiXianNvStrategyImpl implements NiuLangVisitStrategy {
    @Override
    public String visitGirl() {
        return "牛郎访问了七仙女";
    }
}
```

## 问题分析

我们分析这个情况用策略模式有二个弊端: 一个是类的数量会爆炸性增长, 二是不利于代码的复用

一是本来牛郎这个策略是用的非常好的, 但是对董永和刘昌彦不是很好, 这个时候董永也想去串门了, 他又实现了一个 DongyongVisitStrategy 的三个类:

```java
public interface DongYongVisitStrategy {
    String visitGirl();
}
```

董永访问织女:

```java
public class DongYongVisitZhiNvStrategyImpl implements DongYongVisitStrategy {
    @Override
    public String visitGirl() {
        return "董永访问了织女";
    }
}
```

董永访问七仙女:

```java
public class DongYongVisitQiXianNvStrategyImpl implements DongYongVisitStrategy {
    @Override
    public String visitGirl() {
        return "董永访问了七仙女";
    }
}
```

董永访问三圣母:

```java
public class DongYongVisitSanShengMuStrategyImpl implements DongYongVisitStrategy {
    @Override
    public String visitGirl() {
        return "董永访问了三圣母";
    }
}
```

这时候刘昌彦也想去,,, 这就变成 9 个类了, 这用策略模式就有些勉强了, 我们经过一阵复杂的排序组合知识, 预计类的数量是 m\(凡人数量\)\*n\(仙女数量\) 这么多, 这个时候, 如果又下凡了一个仙女, 类的数量就是 4\*4 = 16 个了

二是如果 "访问了" 三个字在生产环境里是成百上千的代码, 那么重复的代码就会很多, 一旦变更, 需要在多个地方变更

## 桥接模式

我们定义找个二个可能变化的维度: A 访问了 B, A 的变化 B 不应该感知, B 的变化 A 也不需要感知

定义二个变化维度的接口:

女孩:

```java
public interface Girl {
    String queryName();
}
```

男孩:

```java
public interface Body {

    default String action() {
        return "访问了";
    }

    default String visitGirl(Girl girl){
        return queryName() + action() + girl.queryName();
    }

    String queryName();
}
```

实现三个女孩:

```java
public class QiXianNvGirl implements Girl {
    @Override
    public String queryName() {
        return "七仙女";
    }
}
public class SanShengMuGirl implements Girl {
    @Override
    public String queryName() {
        return "三圣母";
    }
}
public class ZhiNvGirl implements Girl {
    @Override
    public String queryName() {
        return "织女";
    }
}
```

实现三个男孩:

```java
public class DongYongBody implements Body {
    @Override
    public String queryName() {
        return "董永";
    }
}
public class LiuChangYanBody implements Body {
    @Override
    public String queryName() {
        return "刘昌彦";
    }
}
public class NiuLangBody implements Body {
    @Override
    public String queryName() {
        return "牛郎";
    }
}
```

使用:

```java
System.out.println(new NiuLangBody().visitGirl(new ZhiNvGirl()));
```

我们成功把 m \* n 个类 变成了 m + n 个类, 并且代码的可复用性提高了很多

