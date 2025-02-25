## 目的

可以屏蔽掉不同类的区别, 标记某些属性的不同从而对这些属性统一处理

## 例子代码

最近上海漫展一位身着大胆的女孩去报警, 因为自己身着 jk 制服参加漫展, 在地上做出各种姿势的时候走光被人偷拍, 并被职责污染了 jk 的圈子, 同时还有一则互联网届的新闻, 由于 sonar 服务器配置问题, 多个知名的互联网企业的代码都有泄露, 微软小米任天堂迪士尼等

[https://v.qq.com/x/page/y3127ka8n1t.html](https://v.qq.com/x/page/y3127ka8n1t.html)

比如我们要初步描述这两个事件:

定义一个企业类:

```java
@Data
@AllArgsConstructor
public class Enterprise {
    private String name;
    private String codeHubName;
}
```

定义一个 jk 女孩类:

```java
@Data
@AllArgsConstructor
public class JkGirl {
    private String name;
    private String cloth;
    //安全裤,,,
    private String anquanku;
}
```

定义一个泄露的方法:

```java
public void printLeakageInfo(Object object) {
    if(object instanceof Enterprise) {
        System.out.println(
            "泄露了" + ((Enterprise)object)
                .getCodeHubName());
    }
    else if(object instanceof JkGirl) {
        System.out.println(
            "泄露了" + ((JkGirl)object)
                .getAnquanku());
    }
}
```

使用:

```java
public static void main(String[] args) {
    Enterprise enterprise =
        new Enterprise("微软",
            "Windows代码库");
    JkGirl jkGirl =
        new JkGirl("jkGirl",
                    "白色上衣",
                 "黑色安全裤");
    printLeakageInfo(enterprise);
    printLeakageInfo(jkGirl);
}
```

输出:

```java
泄露了Windows代码库
泄露了黑色安全裤
```

## 问题分析

printLeakageInfo 方法接收了不同的对象, 都要处理 if-else, 每次增加一种类型, 都需要增加一个 if-else, 这样的方法是很难维护的

## 属性注解模式

定义一个泄露的属性注解

```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Leakage {

}
```

在不同类的属性上打上标签:

```java
@Data
@AllArgsConstructor
public class Enterprise {
    private String name;
    @Leakage
    private String codeHubName;
}
```

```java
@Data
@AllArgsConstructor
public class JkGirl {
    private String name;
    private String cloth;
    //安全裤,,,
    @Leakage
    private String anquanku;
}
```

重新写输出方法:

```java
Field[] declaredFields = object.getClass()
    .getDeclaredFields();
for (Field declaredField : declaredFields) {
    if(!declaredField
        .isAnnotationPresent(Leakage.class)) {
        continue;
    }
    declaredField.setAccessible(true);
    System.out.println("泄露了" 
                    + declaredField.get(object));
}
```

重新调用这个方法的输出也是和上面一样的

## 课后作业

1. 定义一个方法 printObject\(Object object\) 方法, 打印的时候只打印某些不敏感的属性
2. 定义一个 @ExcelTitle 注解, 再定义一个 exportExcel\(Object object\) 方法用来导出带有表头的 excel
    



