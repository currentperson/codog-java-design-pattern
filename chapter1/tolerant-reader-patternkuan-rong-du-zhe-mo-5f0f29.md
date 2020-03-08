## 目的

依赖的系统改变了自己的模型, 我们的系统尽量不崩掉

## 例子代码

女神有很多属性, 我们只需要读必要的属性, 要想生活过得去, 头上总得有点绿.

```java
@Data
@AllArgsConstructor
public class GirlGod {

    private String name;

    private LocalDate birthday;

    private String weChatId;

    //是否正在学英语
    private Boolean makingLove;
}
```

我们这边只需要女生的这些信息:

```java
@Data
public class MyGirlGod {

    private String name;

    private LocalDate birthday;

    private String weChatId;
}
```

当我们反序列化读取女神的信息时候:

```java
public class Me {

    public static MyGirlGod tolerantReading(String girlStr) {
        return GsonUtil.convert(girlStr, MyGirlGod.class);
    }

    public static void main(String[] args) {
        System.out.println(Me.tolerantReading(GsonUtil.toJson(new GirlGod("兰兰", LocalDate.now(), "13820802870", true))));
    }
}
```

这个时候, 及时女神的某些属性是空或者多增加了一些属性, 不会导致我们的系统崩掉.

## 更进一步

这个模式的缺点我正好这二天排查问题遇到了, 如果其他系统返回的是非正常数据, 比如返回了一个毫不相关的错误 json, 这个也会导致程序的不确定性, 需要对核心字段转换之后再进行校验, 比如系统的不是

```java
{"name":"兰兰","birthday":{"year":2020,"month":3,"day":8},"weChatId":"13820802870","makingLove":true}

```

而是:

```java
{"errorCode":"5000","message":"错误信息"}
```

上面的代码仍然能够跑, 但是会遇到不可预测的问题.

