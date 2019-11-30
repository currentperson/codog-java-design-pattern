## 目的

使得客户端不必要关心产品的构造的细节

## 例子代码

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

## 简单工厂模式











