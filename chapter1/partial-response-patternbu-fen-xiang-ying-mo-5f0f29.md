## 目的

客户端指定自己需要的数据, 从服务端只向客户端传输必要的数据

## 例子代码

马上要到年底了, 又要紧急相亲了, 想起我这相亲史不禁潸然泪下, ~~输给叙利亚的~~从未赢过的国足版差强人意

![](/assets/2019111601.png)

遥想那是我第一次相亲, 我问 "你多大", 她说 C, 第二次相亲的问我: "你多大", 我说 20 ~~CM~~, 第三次 ..., 以上都是玩笑了, 其实相亲也没那么难, 只要你穿着整洁, 举止礼貌, 走的时候记得开劳斯莱斯送人家回去就行了

![](/assets/2019111600.png)

一个男生有很多属性, 常见的身高, 体重, ~~肺活量~~, 学历, 性格, 是否有车, 有房, 有钱等, 比如我们这个男生是一个大类, 里面的属性都是存在一个表中或者都是从一个底层接口取的, 这时候我们定义一个接口返回男生的信息:

```java
@Data
public class Boy {

    private String name;

    private Integer height;

    private Integer weight;

    private CharacterTypeEnum characterType;

    private Boolean hasCar;

    private Boolean hasHouse;

    private Boolean rich;
}
```

性格枚举内容如下:

```java
@Getter
public enum CharacterTypeEnum {

    MATURE_STEADY("成熟稳重"),
    HUMOROUS("风趣幽默");

    CharacterTypeEnum(String desc) {
        this.desc = desc;
    }

    private String desc;
}
```

我们用代码模拟一下这个能力:

```java
public class RestClient {

    public static final Boolean[] 
        BOOLEAN_ARRAY = {Boolean.TRUE, Boolean.FALSE};

    public static final String[] 
        NAME_ARRAY = {"贾宝玉", "武松", "张角"};
    public static final Integer[] 
        HEIGHT_ARRAY = {170, 175, 180};
    public static final Integer[] 
        WEIGHT_ARRAY = {50, 60, 70};

    public static final Random 
        RANDOM = new java.util.Random();

    public static Boy queryBoyDetail() {
        Boy boy = new Boy();
        boy.setName(queryRandomItem(NAME_ARRAY));
        boy.setHeight(queryRandomItem(HEIGHT_ARRAY));
        boy.setWeight(queryRandomItem(WEIGHT_ARRAY));
        boy.setCharacterType(
        queryRandomItem(CharacterTypeEnum.values()));
        boy.setHasCar(queryRandomItem(BOOLEAN_ARRAY));
        boy.setHasHouse(queryRandomItem(BOOLEAN_ARRAY));
        boy.setRich(queryRandomItem(BOOLEAN_ARRAY));
        return boy;
    }

    public static <T> T queryRandomItem(T[] itemArray) {
        return itemArray[RANDOM.nextInt(itemArray.length)];
    }
}
```

我们对外定义的接口如下:

```java
public class RestService {

    public static Boy queryMyBoyInfo() {
        return RestClient.queryBoyDetail();
    }
}
```

## 问题分析

我们假如不同的相亲对象有不同的要求, A 女想要知道的是身高, 学历, 是否有钱, B 女想知道的时候是性格, 有房, 是否有钱,C 女想知道是否有钱

![](/assets/2019111603.png)

可以只返给用户需要的数据, 这样用户不会很困惑\(找不到自己需要的字段或者对其他字段的含义疑惑\), 也可以避免无用的数据传输的网络带宽, 降低网络传输耗时和提高传输的稳定性

## 部分响应模式

我们定义一个通用的支持部分响应模式

```java
public class FieldJsonMapper {

    public static <T> String toJson
        (T t, String[] fields, Class<T> tClass) 
            throws Exception {
        StringBuilder json = new StringBuilder()
                                .append("{");

        for (int i = 0, fieldsLength = 
            fields.length; i < fieldsLength; i++) {
            json.append(getString(t, 
                tClass.getDeclaredField(fields[i])));
            if (i != fieldsLength - 1) {
                json.append(",");
            }
        }
        json.append("}");
        return json.toString();
    }

    private static <T> String getString(T t, 
        Field declaredField) 
            throws IllegalAccessException {
        declaredField.setAccessible(true);
        Object value = declaredField.get(t);
        if (declaredField.get(t) 
                            instanceof Integer) {
            return "\"" + declaredField.getName() 
                                + "\"" + ": " + value;
        }
        return "\"" + declaredField.getName() 
        + "\"" + ": " + "\"" + value.toString() + "\"";
    }
}
```

我们定义这样一个对外的接口:

```java
public static String queryMyBoyInfoByFields(String[] fields) 
                                         throws Exception {
        Boy boy = RestClient.queryBoyDetail();
        return FieldJsonMapper.toJson(boy, fields, Boy.class);
}
```

输出:

```java
{"name": "贾宝玉","rich": "false","height": 180}
```

## 生产例子

我觉得这种模式主要用于给前端提供接口的时候使用, 当时重构做xx贷首页的时候, 业务不熟悉, 输出了很多无用的字段, 导致首页打开都有 10 s 的了, 现在想想这里面有我很大的"功劳", 听说现在 P8 + P7 墨在重写我的代码呢, 希望墨爷看我的代码可以~~学到我的设计模式~~\(不骂我\)

## 课后作业

1. 谈谈部分响应模式的局限性和缺点
2. 我们在生产上有一个用户信息接口, 返回值是:

```java
@Data
public class UserInfo {

    private String name;

    private Integer level;

    private LocalDateTime registerDate;

    private String email;

    private String phone;
}
```

现在要求对外开一个包含姓名, 用户等级的接口

![](/assets/2019111604.png)



## 后言

参考: [https://java-design-patterns.com/patterns/partial-response/](https://java-design-patterns.com/patterns/partial-response/)

