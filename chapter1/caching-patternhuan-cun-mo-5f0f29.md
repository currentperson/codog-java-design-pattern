## 目的

缓存降低耗时接口的结果, 重复查询时候可以减少时间消耗

## 例子代码

最近 &lt;乘风破浪的姐姐们&gt; 开播,  我作为一名资深宅男, 肯定不会放过这个综艺, 当我们想要查询根据 id 获取每个姐姐的信息的时候, 可能会比较耗时, 而这些信息基本不会变, 这时候就是缓存模式出场了.

## 缓存模式

定义一个查询:

```java
public interface QueryAction<T, R> {

    T queryFromCache(String key);

    T query(R r);

    void putToCache(String key, T t);
}
```

定义一个抽象的缓存实现:

```java
public abstract class AbstractQueryAction<T, R> implements QueryAction<T, R> {

    @Override
    public T queryFromCache(String key) {
        return (T) App.CACHE_MAP.get(key);
    }

    @Override
    public void putToCache(String key, T t) {
        App.CACHE_MAP.put(key, t);
    }
}
```

定义一个模板:

```java
public class CacheTemplate {
    public <T, R> T queryByCaching(QueryAction<T, R> queryAction, R r, String key) {
        T resultFromCahce = queryAction.queryFromCache(key);
        if (resultFromCahce != null) {
            return resultFromCahce;
        }
        T result = queryAction.query(r);
        queryAction.putToCache(key, result);
        return result;
    }
}
```

这里有一个比较耗时的方法:

```java
public class UserService {

    @SneakyThrows
    public static String queryUserName(String userId) {
        Thread.sleep(1000);
        if(Objects.equals("1",userId)) {
            return "黄圣依";
        }
        if(Objects.equals("2",userId)) {
            return "伊能静";
        }
        if(Objects.equals("3",userId)) {
            return "张雨绮";
        }
        if(Objects.equals("4",userId)) {
            return "宁静";
        }
        return "codog代码狗";
    }
}
```

使用:

```java
System.out.println(cacheTemplate.queryByCaching(new AbstractQueryAction<String, String>() {
    @Override
    public String query(String userId) {
        return UserService.queryUserName(userId);
    }
}, "123", "userid-123"));
```

## 课后作业

1. 将抽象缓存类换成 redis 实现
2. 思考如何保证缓存与接口的一致性
3. 了解缓存雪崩, 缓存穿透, 缓存击穿, 并改进文中的代码



