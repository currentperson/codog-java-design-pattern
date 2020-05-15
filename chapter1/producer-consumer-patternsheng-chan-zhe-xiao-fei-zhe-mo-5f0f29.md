## 目的

效率型设计模式, 意义在于不阻塞主流程的同时完成异步操作, 比如发邮件短信, 完成耗时的任务等

## 例子代码

最近蜂巢收费 5 毛的事件引起了业主和物业的强烈反对, 5 毛钱其实也不是很多, 掉地上有人都不看一眼的, 也就我们这种中年男人才会捡起来揣兜里, 我们先来看看没有蜂巢的时候我们的快递其实是比较不方便的, 我们假设快递物品必须要交到收货人手里, 不然可能会有丢失风险.

先定义一个货物类:

```java
@Data
@AllArgsConstructor
//货物
public class Good {
    private String name;
}
```

比如我们定义一个消费者:

```java
@Data
@AllArgsConstructor
//'消费者'
public class Consumer {

    private String name;

    public void receiveGood(List<Good> goodList) {
        back2Home();
        System.out.println(name + "收到了" + goodList.toString());
    }

    //返回家中
    @SneakyThrows
    private void back2Home() {
        Thread.sleep(1000);
    }
}
```



