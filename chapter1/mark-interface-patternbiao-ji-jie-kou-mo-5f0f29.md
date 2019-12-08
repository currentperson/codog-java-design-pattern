## 目的

标记一个类拥有某个能力或者使得其与其他类区分开

## 例子代码

这世上男人的身高和女人的体重是不可以被大家知道的, 所以别人问的时候我们都需要进行加密/脱敏, 比如我一米~~八~~\(吧\)并没有到处说.

![](/assets/2019120801.png)                                        ![](/assets/2019120802.png)

标记接口区分于其他的接口, 这种接口没有需要覆写的方法, 也没提供基础的通用能力.

我们定义一个 Girl 类:

```java
@Data
@AllArgsConstructor
public class Girl {

    private String name;

    private Cup cup;
}
```

Cup 类如下:

```java
@Data
@AllArgsConstructor
public class Cup implements NotPrint {

    private CupEnum cupType;

    private Integer bustSize;
}
```

NotPrint 标记接口如下:

```java
public interface NotPrint {
}
```

我们的日志工具类可以如下:

```java
public class LoggerUtil {

    public static void printWithoutCupInfo
        (Object object) throws IllegalAccessException {
        for (Field field : object.getClass()
                                .getDeclaredFields()) {
            field.setAccessible(true);
            Object filedValue = field.get(object);
            if (filedValue instanceof NotPrint) {
                continue;
            }
            System.out.println(field.getName() + 
                                    ":" + filedValue);
        }
    }
}
```

使用方法:

```java
public class App {

    public static void main(String[] args) 
                    throws IllegalAccessException {
        LoggerUtil.printWithoutCupInfo
                (new Girl("兰兰",new Cup(CupEnum.A,60)));
    }
}
```

类图如下:

![](/assets/2019120803.png)

## 生产例子

```java
java.io.Serializable
java.lang.Cloneable
```

## 课后作业

1. 定义一个 Employee 员工类, 打印 日志/落库 的时候将 Money 类的属性省略

```java
@Data
public class Employee {

    private String name;

    private Integer level;

    private Money bonus;

    private Money wages;
}
```

1. 使用 Annotation 替换文中的例子, 并说明 Annotation 的优点



