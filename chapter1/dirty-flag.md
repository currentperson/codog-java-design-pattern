## 目的

避免不必要的耗时的重复计算

## 代码例子

福报厂实现 P8 以下全员 35 以下, 菊花厂清理 35 岁以下员工, 各个大厂裁员, 招聘要求低于 35 岁等, 各种真真假假的新闻让我们这种互联网人如坐针毡, 危机感满满, 像我这种刚 18 岁就要担心 35 岁的日子了, 貌似每个天天坐在办公室的互联网人到了 35 岁只能都去送快递, 送外卖了\(也不知道能不能应聘得上\)

比如这天公司的老板叫你每天给他统计以下公司的平均年龄, 来作为优\(cai\)化\(yuan\)的依据, 我们目前只有一个组织结构树和一个根据员工工号查询年龄的接口, 所以我们的代码可能会这么写:

先定义一个根据员工 ID 查询员工年龄的接口:

```java
public class EmployeeWebService {

    public static Integer 
        queryEmployeeAge(String userId) {
        return Integer.valueOf(userId);
    }
}
```

定义一个树节点来存储员工结构:

```java
@Data
public class TreeNode<T> {
    private T value;
    private List<TreeNode<T>> 
        children = new ArrayList<>();

    public TreeNode(T value) {
        this.value = value;
    }
}
```

计算员工的平均年龄:

```java
public static Integer queryEmployeeAvgAge
    (TreeNode<String> employeeUserTree) {
    Integer totalAge = 0;
    Integer employeeCount = 0;
    if (employeeUserTree == null) {
        System.out.println("公司倒闭了");
        return 0;
    }
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(employeeUserTree);
    while (!queue.isEmpty()) {
        TreeNode<String> node = queue.poll();
        totalAge += EmployeeWebService.
            queryEmployeeAge(node.getValue());
        employeeCount++;
        for (TreeNode<String> child : 
                        node.getChildren()) {
            queue.offer(child);
        }
    }
    return (totalAge / employeeCount);
}
```

## 脏标记模式

因为是个树层结构, 遍历很好耗时, 每个员工还发生了一次 RPC 调用来查询年龄, 但是如果每天都需要计算, 如果这个公司的人员变动不大, 一天内没有人入职和离职, 可以使用前一天的数据, 这个就是使用脏标记模式

我们先来定义二个变量, 实际应用中可能存储在数据库, 文件或者缓存中:

```java
public class Constant {
    public static Boolean 
        IS_EMPLOYEE_CHANGED = false;
    public static Integer 
        LASTDAY_EMPLOYEE_AVG_AGE = 0;
}
```

比如定义了一个员工入职离职的接口:

```java
public static void changeEmploy(String userId) {
    Constant.IS_EMPLOYEE_CHANGED = true;
}
```

这些改动之后我们的代码:

```java
public static Integer queryEmployeeAvgAge
    (TreeNode<String> employeeUserTree) {
    if(!Constant.IS_EMPLOYEE_CHANGED) {
        return Constant.LASTDAY_EMPLOYEE_AVG_AGE;
    }
    //一样的计算年龄的diamante
    int age = totalAge / employeeCount;
    Constant.LASTDAY_EMPLOYEE_AVG_AGE = age;
    return age;return (totalAge / employeeCount);
}
```

## 类图:

![](/assets/2019110900.png)

## 课后作业

1. 找出例子程序中的风险点代码
2. 找出生产场景下的例子
3. 使用模板方法模式实现脏标记模式



