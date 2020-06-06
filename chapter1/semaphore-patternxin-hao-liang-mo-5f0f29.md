## 目的

保护一组资源中的每个资源都不会受到并发访问而导致不一致

## 例子代码

男: 砸了你的摊, 逼着你离开, 最后知道真相的我眼泪掉下来, 砸了你的摊, 我背了良心债, 就算付出再多感情也再买不回来

![](/assets/2020060600.png)    ![](/assets/2020060601.png)

女: 当初是你要分开, 分开就分开, 如今又要用真\(政\)爱\(策\), 把我哄回来, 摆摊不是你想摆, 想摆就能摆, 让我挣开, 让我明白, 放手你的爱

摆摊经济一出, 各国人都开启了摆摊之旅:

![](/assets/2020060605.png) ![](/assets/2020060603.png) ![](/assets/2020060604.png)

可是摊位有限, 不能随便摆哦, 我们来讲讲这里面的故事:

我们定义一下货物类:

```java
//商品
@Data
@AllArgsConstructor
public class Goods {
    private String name;
}
```

定义一个摊位类:

```java
//摊位
@Data
@AllArgsConstructor
public class Stall {
    private Integer number;
    private String name;
    private List<Goods> saleGoods;
}
```

定义一个摆摊的人:

```java
//摆摊人
@Data
@AllArgsConstructor
public class People {
    private String name;
    private List<Goods> saleGoods;
}
```

我们三个人同时去摆摊:

```java
List<People> peopleList = new ArrayList<>();
peopleList.add(new People("小李", Arrays.asList(new Goods("小李皮鞋"), new Goods("小李运动鞋"))));
peopleList.add(new People("小王", Arrays.asList(new Goods("小王西瓜"), new Goods("小王椰子"))));
peopleList.add(new People("小张", Arrays.asList(new Goods("小张肥皂"), new Goods("小张洗衣液"))));
List<Stall> stallList = new ArrayList<>();
stallList.add(new Stall(1,"摊位一",new ArrayList<>()));
stallList.add(new Stall(2,"摊位二",new ArrayList<>()));
peopleList.parallelStream().forEach(
        (people) -> {
            for (Stall stall : stallList) {
                if(stall.getSaleGoods().isEmpty()) {
                    stall.setName(people.getName() + "的摊位");
                    //拜访货物中
                    putGoods2Stall();
                    stall.setSaleGoods(people.getSaleGoods());
                }
            }
        }
);
System.out.println(stallList.toString());
```

输出:

```java
[Stall(number=1, name=小李的摊位, saleGoods=[Goods(name=小李皮鞋), Goods(name=小李运动鞋)]), Stall(number=2, name=小王的摊位, saleGoods=[Goods(name=小张肥皂), Goods(name=小张洗衣液)])]
```

## 问题分析

这个和互斥模式是一样的, 主要是

