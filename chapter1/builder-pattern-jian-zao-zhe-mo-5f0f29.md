# Builder Pattern\(建造者模式\)

## 目的

1. 减少构造函数的数量, 去除参数过多的构造函数, 参数过多会引起可读性和易用性下降
2. 将某个构造耗时的部分异步构造, 等到真正用到的时候再完全构造出来
3. 某个复杂类的某个部分的构造实现和各个部分的构造顺序分离开, 使得各个部分的既能按照一定的顺序构建, 又能对调用方屏蔽顺序细节

## 例子代码

假如我们要建造个女朋友\(心疼自己, 别人的女朋友不都是同学, 相亲找的么, 我的怎么还需要自己 new\)

女朋友有很多属性, 年龄, ~~性别~~, \(还要有性别, 心疼自己, 带不带物种呀, 删删删\), 姓名等, 不同阶段我们可能知道不同的属性, 所以我们将女朋友类设计如下:

### 初始实现

```java
@Getter
@Setter
public class GirlFriend {

    /**
     * 出生日期
     */
    private LocalDateTime birthDay;

    /**
     * 姓名
     */
    private String name;

    /**
     * 星座
     */
    private ConstellationEnum constellation;

    /**
     * 家乡
     */
    private String hometown;

    /**
     * 身高
     */
    private Integer height;

    /**
     * 体重
     */
    private Integer weight;

    /**
     * 凶兆
     */
    private CupEnum cup;

    /**
     * 刚认识, 只知道姓名
     * @param name
     */
    @BadSmell
    public GirlFriend(String name) {
        this.name = name;
    }

    /**
     * 开始约会聊天, 开始查户口
     * START: 你家哪的呀
     *
     * G: 内蒙古
     * I: 大草原, 骑马上学, 考试考射箭
     * G: 福建
     * I: 卖茶的
     * G: 北京
     * I: 你们那雾霾走路上看不到人吧
     * G: 四川
     * I: 你们那辣呀
     * G: 合肥
     * I: ... 合肥有啥
     * @param birthDay
     * @param name
     * @param constellation
     * @param hometown
     * @param height
     * @param weight
     */
    @BadSmell
    public GirlFriend(LocalDateTime birthDay, String name, ConstellationEnum constellation, String hometown, Integer height, Integer weight) {
        this.birthDay = birthDay;
        this.name = name;
        this.constellation = constellation;
        this.hometown = hometown;
        this.height = height;
        this.weight = weight;
    }

    /**
     * 开始深入聊天 ...
     * 不然呢, 你以为最后一个入参怎么知道的
     * 什么? 什么摸, 摸什么的
     * @param birthDay
     * @param name
     * @param constellation
     * @param hometown
     * @param height
     * @param weight
     * @param cup
     */
    @BadSmell
    public GirlFriend(LocalDateTime birthDay, String name, ConstellationEnum constellation, String hometown, Integer height, Integer weight, CupEnum cup) {
        this.birthDay = birthDay;
        this.name = name;
        this.constellation = constellation;
        this.hometown = hometown;
        this.height = height;
        this.weight = weight;
        this.cup = cup;
    }
}
```

### 问题分析

我们觉得有三个问题

一. 构造函数数量太多啦

如果我们是个老司机, 我们久经沙场, 一眼就能看出女朋友的 cup, 我们就可能再增加一个构造函数如下:

```java
    /**
     * 老司机构造函数
     * @param name
     * @param cup
     */
    @BadSmell
    public GirlFriend(String name, CupEnum cup) {
        this.name = name;
        this.cup = cup;
    }
```

如果我们是朋友介绍的, 说是个年轻小姑娘, 才18 岁\(洗洗睡吧, 18 岁的小姑娘看不上你的\), 我们就知道她的年龄, 甚至不知道姓名, 我们就得再增加一个构造函数

```java
    /**
     * 朋友介绍, 只说了是个 18 岁的姑娘
     * @param birthDay
     */
    @BadSmell
    public GirlFriend(LocalDateTime birthDay) {
        this.birthDay = birthDay;
    }
```

二. 第三个的入参太多啦, 身高体重还都是 Integer 类型, 一个填错就把 168 cm, 50 kg 的女票变成了 50 cm, 168 kg 的女朋友\(再次心疼自己, 168 kg 的都没找到\)![](/assets/2019091403.png)

三. 对新属性的扩展性极差

如果我们今天新想到了一个属性需要记录下来, 比如女朋友职业\(学生, 白领, 文员, 程序媛, 教师\), 比如血型等等, 我们每次增加第 n 个属性属性, 如果想要全部构造出来, 可能需要添加 2^\(n - 1\) 构造函数, 这个是很绝望的

### 初步尝试解决

~~看了上面的分析, 我们灵光乍现, 既然想使用构造函数构造个女朋友这么复杂, 那我们就不构造了在生活中找一个不就好啦~~

我们既然是个优秀的程序员, 解决这个问题比在生活中找一个女朋友容易多啦\(至少构造函数不嫌我们脱发呀\)

好, 那应该怎么解决呢, 既然构造函数数量以指数级别膨胀, 那我们不用构造函数了行不行, 使用无参构造函数和 Set 方法写下怎么样呢

```java
    /**
     * 使用无参构造函数和 Setter 完成对象拼装
     */
    public static void buildGirlFriendWithSetter() {
        GirlFriend girlFriend = new GirlFriend();
        girlFriend.setBirthDay(LocalDateTime.now());
        girlFriend.setName("");
        girlFriend.setConstellation(null);
        girlFriend.setHometown("");
        girlFriend.setHeight(0);
        girlFriend.setWeight(0);
        girlFriend.setCup(null);
        ...
    }
```

## 生产实践

可以使用一些插件来帮助我们完成重复开发的工作, IDEA 插件的安装请参考 [IDEA 安装插件的方法](/idea-an-zhuang-cha-jian-de-fang-fa.md)

### 使用 Builder 插件

插件地址: [https://plugins.jetbrains.com/plugin/6585-builder-generator/](https://plugins.jetbrains.com/plugin/6585-builder-generator/)

### 使用 Lombok 插件

插件地址: [https://plugins.jetbrains.com/plugin/6317-lombok/](https://plugins.jetbrains.com/plugin/6317-lombok/)

