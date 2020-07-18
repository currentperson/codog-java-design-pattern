## 目的

为实际访问的对象增加控制能力

## 例子代码

最近专利小学生火了, C10orf67 在结直肠癌发生发展中的功能与机制研究, 这标题他都没看懂拿去参赛获奖了, 还有近期厦大和天大的同学毕业论文大部分段落重复的问题, 还有之前的校长气功治疗癌症争议, 不禁让人感慨, 少年强则国强, 少年假则国假, 希望以后学术氛围会越来越好.

我们定义一个 Owner 接口代表论文的编写人:

```java
public interface Owner {

    String writePaper(String condition);
}
```

真实写论文的人:

```java
public class RealOwner implements Owner {

    @Override
    public String writePaper(String condition) {
        return condition + "参赛文章: <C10orf67 在结直肠癌发生发展中的功能与机制研究>";
    }
}
```

我们拿着去参加博士生的比赛:

```java
Owner owner = new RealOwner();
System.out.println(owner.writePaper("博士大赛"));
```

输出:

```java
博士大赛参赛文章: <C10orf67 在结直肠癌发生发展中的功能与机制研究>
```

## 问题分析

不是呀, 我是给我儿子去参加小学生竞赛的, 怎么参加了博士大赛, 我这个 RealOwner 类的方法需要我儿子来代理访问

## 代理模式

所以我们让小学生代理我们去参赛, 不是小学生的比赛不参加

```java
public class ProxyOwner implements Owner {
    private Owner myFather = new RealOwner();

    @Override
    public String writePaper(String condition) {
        if (Objects.equals("云南小学生大赛", "")) {
            return myFather.writePaper(condition);
        }
        return "不好意思, 不是小学生比赛不参加";
    }

    private class RealOwner implements Owner {

        @Override
        public String writePaper(String condition) {
            return condition + "参赛文章: <C10orf67 在结直肠癌发生发展中的功能与机制研究>";
        }
    }
}
```

这样就是让客户端在使用的时候确保不是小学生的比赛不参加了

```java
Owner owner = new ProxyOwner();
System.out.println(owner.writePaper("博士大赛"));
```

输出:

```java
不好意思, 不是小学生比赛不参加
```

类图:

![](/assets/2020071800.png)

