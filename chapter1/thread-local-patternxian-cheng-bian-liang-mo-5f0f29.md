## 目的

减少方法入参, 一次请求处于相同的上下文中

## 例子代码

最近 Kevin 刚从迪士尼跳槽到字节跳动, 还没有过试用期就离职了, 安倍晋三也由于身体原因辞去日本首相的工作

我们先创建一个 ThreadLocal 的实例:

```java
public static final 
ThreadLocal<User> INTEGER_THREAD_LOCAL 
= new ThreadLocal<>();
```

我们在多个线程中描述这个事情:

```java
IntStream.range(1, 10)
    .parallel().forEach(i -> {
    try {
        final User user = new User();
        user.setName("凯文·梅耶尔" + i);
        user.setPlace("字节跳动");
        INTEGER_THREAD_LOCAL.set(user);
        print();
    } finally {
        INTEGER_THREAD_LOCAL.remove();
    }
});
```

其中 print 方法的实现是:

```java
public static void print() {
    System.out.println(
        INTEGER_THREAD_LOCAL.get()
            .getName() + "从" +
            INTEGER_THREAD_LOCAL.get()
                .getPlace() + "离开");
}
```

最终输出:

```java
凯文·梅耶尔3从字节跳动离开
凯文·梅耶尔9从字节跳动离开
凯文·梅耶尔2从字节跳动离开
凯文·梅耶尔7从字节跳动离开
凯文·梅耶尔5从字节跳动离开
凯文·梅耶尔4从字节跳动离开
凯文·梅耶尔6从字节跳动离开
凯文·梅耶尔1从字节跳动离开
凯文·梅耶尔8从字节跳动离开
```

一次请求可能会调用非常多的方法, 这些上下文比如 traceId, userId 之类的都是好多接口都需要使用的信息, 如果每个接口都提供这些的字段, 将会非常难以维护和调用这些代码

## 课后作业

1. 了解 ThreadLocal 底层的数据结构
2. 在从网关或者前端发送过来的请求, 将用户信息存储在 ThreadLocal 中消费
3. 思考如何微服务 rpc 如何达到类似于 ThreadLocal 这样的系统上下文



