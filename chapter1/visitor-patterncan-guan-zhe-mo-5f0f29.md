## 目的

为一组各种数据提供同一个维度的访问方法

## 例子代码

最近360裁员又是一波新闻, 其实近年来互联网公司裁员也是司空见惯了, 感觉应该是经济大环境不好加上互联网越来越冷静了, 感觉上除了头条和拼多多还能逆势扩招, 其他大厂明里暗里裁员, 最好的也就还有少量招聘名额...

在这种情况下, 比如我们要统计加班时长裁员, 交到我们手里可能会这么写:

先定义一个员工类:

```java
@Data
public abstract class Employee {
    //工作时长
    protected Integer workingHours;
    //薪水
    protected BigDecimal salary;
}
```

然后定义三种员工:

程序员:

```java
@Data
public class Coder extends Employee {
    //加班时长
    private Integer overtimeHours;

    public Coder(Integer workingHours, BigDecimal salary, Integer overtimeHours) {
        this.workingHours = workingHours;
        this.salary = salary;
        this.overtimeHours = overtimeHours;
    }
}
```

人力资源:

```java
@Data
public class Hr extends Employee {
    private Integer resumeCount;

    public Hr(Integer workingHours, BigDecimal salary, Integer resumeCount) {
        this.workingHours = workingHours;
        this.salary = salary;
        this.resumeCount = resumeCount;
    }
}
```

销售人员:

```java
public class Salesman extends Employee {
    public Salesman(Integer workingHours, BigDecimal salary) {
        this.workingHours = workingHours;
        this.salary = salary;
    }
}
```

程序员呢, 工作时间是根据正常上班时间+加班时间算的, hr 是是需要加加上每个简历处理二个小时, 销售人员呢, 需要根据实际情况加随机的 8 个小时

```java
public static void main(String[] args) {
    Integer totalWorkHours = 0;
    for (Employee employee : EMPLOYEE_LIST) {
        if (employee instanceof Coder) {
            totalWorkHours += employee.getWorkingHours() + ((Coder) employee).getOvertimeHours();
        }
        if (employee instanceof Hr) {
            totalWorkHours += employee.getWorkingHours() + ((Hr) employee).getResumeCount()*2;
        }
        totalWorkHours += employee.getWorkingHours() + new Random().nextInt(8);
    }
    System.out.println("总时长: " + totalWorkHours);
}
```

然后我们使用这个方法计算员工实际工作时间, 就可以输出固定的结果了

## 问题分析

这个里面有几个问题, 一个是计算员工耗时的方法, 如果新增一种员工类型, 需要再加个 if-else

另外是这个规则可能经常需要变化, 当前是汇报给老板的工时的规则, 比如对外公布的一套规则可能是程序员的工时需要取一半, hr 保持原样, 销售减 2 个小时完

还有可能维度有变化, 比如要统计平均工资, 这些规则如果不是可替换的, 模式客户端直接来访问这种数据结构进行操作, 需求变化, 客户端会很痛苦.

## 参观者模式

**先定义一个参观者类:**

```java
public interface ElementVisitor<R, E, P> {
    R visit(E e, P p);
}
```

**定义一个元素类:**

```java
public interface Element<R, P> {
    R accept(ElementVisitor<R, Element, P> v, P p);
}
```

**员工类改成如下:**

```java
@Data
public abstract class Employee implements Element<Integer,Integer> {
    //默认工作时长,比如 8 小时
    protected Integer workingHours;
    //每小时薪水
    protected BigDecimal salary;

    @Override
    public Integer accept(ElementVisitor<Integer, Element, Integer> v, Integer param) {
        return v.visit(this, param);
    }
}
```

定义三个参观者的泛型屏蔽接口:

```java
public interface RealTimeEmployeeVisitor 
                                extends ElementVisitor<Integer, Employee, Void> {
}
```

**依次定义三个具体的参观者:**

程序员:

```java
public class CoderVisitor implements RealTimeEmployeeVisitor {

    @Override
    public Integer visit(Employee coder, Void param) {
        if (coder instanceof Coder) {
            return coder.getWorkingHours() + ((Coder) coder).getOvertimeHours();
        }
        return 0;
    }
}
```

hr:

```java
public class HrVisitor implements RealTimeEmployeeVisitor {
    @Override
    public Integer visit(Employee hr, Void param) {
        if (hr instanceof Hr) {
            return hr.getWorkingHours() + ((Hr) hr).getResumeCount() * 2;
        }
        return 0;
    }
}
```

销售人员:

```java
public class SalesmanVisitor implements RealTimeEmployeeVisitor {
    @Override
    public Integer visit(Salesman e, Integer param) {
        return e.getWorkingHours() + param;
    }
}
```

**使用方式:**

```java
public static void main(String[] args) {
    Map<Class,ElementVisitor> vistorMap = new HashMap<>();
    vistorMap.put(Coder.class,new CoderVisitor());
    vistorMap.put(Hr.class,new HrVisitor());
    vistorMap.put(Salesman.class,new SalesmanVisitor());

    Integer totalWorkHours = 0;
    for (Employee employee : EMPLOYEE_LIST) {
        totalWorkHours += employee.accept(vistorMap.get(employee.getClass()),null);
    }
    System.out.println("工作总时长: " + totalWorkHours);
```

## 课后作业

1. 完成缺点中的第二个规则的替换
2. 根据 salary 字段计算员工总薪水, 程序员按正常工作时长算, hr 按简历处理时长算, 销售人员按总时长算 



