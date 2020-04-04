## 目的

在执行同一类方法之前 或/和 之后需要做一些相同的操作

## 例子代码

清明时节雨纷纷, 路上行人欲断魂.

今天全国各地都一起哀悼在过去几个月中英勇牺牲的英雄们, 前一段日子公司组织了捐款, 主要是针对一线医护人员的捐助, 是有专门跟踪和公开用途, 虽然有些组织实在是不行, 我还是信得过公司的, 也就贡献了一份力量, 希望为在这场疫情中我们遮风挡雨的一线医护人员一点帮助.

抗击疫情已经取得了阶段性胜利, 大家仍然要注意保护自己, 我们都需要勤洗手, 戴口罩, 每个人洗手的方式可能不一样, 这里我们需求是每个人完成洗手操作, 大概是这么写的:

```java
public interface People {
    void wasHands();
}
```

这里面有二个人的话需要这么写:

```java
public class Codog implements People {

    @Override
    public void wasHands() {
        System.out.println("打开水龙头");
        System.out.println("涂洗手液洗手");
        System.out.println("关闭水龙头");
    }
}
```

加上:

```java
public class Lanlan implements People {

    @Override
    public void wasHands() {
        System.out.println("打开水龙头");
        System.out.println("随便冲冲");
        System.out.println("关闭水龙头");
    }
}
```

使用:

```java
        People people = new Codog();
        people.wasHands();
        people = new Lanlan();
        people.wasHands();
```

输出:

```java
打开水龙头
涂洗手液洗手
关闭水龙头
打开水龙头
随便冲冲
关闭水龙头
```

## 问题分析

很明显, 代码重复了, 既然都需要打开和关闭水龙头, 那肯定要复用这部分代码, 不然新增多少人就需要重复多少次这种代码, 解决这种问题有非常多的办法, 这里我们使用一种相对简单改动比较快的代码.

## 环绕执行模式

```
public interface WashAction {
    void wasHands(String name);
}
```

模板:

```java
public class WashTemplate {
    public static void doWashing(String name, WashAction washAction) {
        System.out.println("打开水龙头");
        washAction.wasHands(name);
        System.out.println("关闭水龙头");
    }
}
```

使用:

```java
WashTemplate.doWashing("兰兰", (name) -> System.out.println(name + "随便冲冲"));
```

效果是一样的, 类图:

![](/assets/2020040400.png)

## 生产例子

触发定时任务的过程都是类似这种:

```java
public class SelectActiveEmployeeCronJob implements SimpleJob {

    @Override
    public void execute(ShardingContext shardingContext) {
        try {
            LoggerUtil.info("executing job.." + "xxxjob");
            jobLauncher.run(userJobConfig.selectActiveEmployeeJob(), defaultBatchJobFactory.createDefaultJobParam());
        } catch (Exception e) {
            LoggerUtil.error("execute job xxxjob failed", e);
        }
    }
}
```

前后都需要打印日志, 并用 try-catch 包围.

可以改成类似操作:

```java
public class JobStartTemplate {

    @Autowired
    private DefaultBatchJobFactory defaultBatchJobFactory;

    public void executeJob(String jobName, ExecuteJobAction executeJobAction) {
        LoggerUtil.info(jobName + " started：{}",new Date());
        try {
            executeJobAction.execute();
        } catch (Exception e) {
            LoggerUtil.error("execute job " + jobName + "failed", e);
        }
    }
}
```

## 课后作业

1. 发送邮件都需要新连接邮件服务器, 发送具体的邮件, 关闭连接, 请用本设计模式实现
2. 除了正常 job, 还有某些特殊类型的 job, 比如开发打开才运行, 比如判断当前时间是否满足条件才运行, 写一个更通用的 ExecuteJobAction
3. 思考: JobStartTemplate 是需要是一个 static 方法 executeJob 还是使用依赖注入方式来调用 executeJob



