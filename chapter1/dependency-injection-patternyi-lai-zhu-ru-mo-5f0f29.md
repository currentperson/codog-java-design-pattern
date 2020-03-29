## 目的

使得一个类的行为由设置的属性类的行为不同而不同

## 例子代码

小时候大家都应该会丢三落四, 及时长大了也经常找不到东西放在哪里, 总是需要问老妈, 妈我袜子放哪了, 妈我鞋放哪了...

我们定义一个我们人这个类, People.

\(一\)People 里面我们可以 new 一个 Mother, 然后调用 mother 类的方法,

\(二\)或者我们可以定义一个方法叫做 callMother\(Mother mother\) ,其中 Mather 类如下:

```java
public interface Mother {

    String findThing();
}
```

在里面调用 Mother 类的方法来输出 callMother 的效果问题分析

## 问题分析

上面的代码一不够灵活, 而且 mother 是调用方法的时候创建, 方法完成后需要垃圾回收, 造成问题, 而且改妈妈还得修改代码, 代码二提供了很好的灵活性, 可是也提供了不必要的灵活性, 同一个孩子需要 call 方法可能会传入好几个妈的情况太少了, 不需要提供这么大的灵活性, 而且妈妈基本上是孩子创建的时候就指定的了, 这就是依赖注入的用处了.

## 依赖注入模式

```java
@Data
public class People {

    private String name;

    private Mother mother;

    public void callMother() {
        System.out.println(this.name + "的妈妈能" + mother.findThing());
    }
}
```

Mother 的二个子类:

```java
public class BagMother implements Mother {

    @Override
    public String findThing() {
        return "找到书包";
    }
}
```

和

```java
public class SockMother implements Mother {

    @Override
    public String findThing() {
        return "找到袜子";
    }
}
```

我们的使用:

```java
public class App {

    public static void main(String[] args) {
        People lanlan = new People();
        lanlan.setName("兰兰");
        lanlan.setMother(new BagMother());
        lanlan.callMother();
    }
}
```



