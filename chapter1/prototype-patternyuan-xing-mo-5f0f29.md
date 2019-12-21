## 目的

通过复制来降低创建复杂对象的时候耗时占用的资源和降低耗时

## 例子代码![](/assets/2019122100.png)

前段日子是南开的大事件, 国家主席来到南开, 南开人也一起庆祝百年校庆, 这次还好我们的校长没有像某京大学的校长鸿浩\(鹄\)之志读个不停, 结果锅从天上来, 现任校长被指涉嫌抄袭, 论文换个图就发了个新论文, 情况大概是这样的:

我们简化论文的结构如下:

```java
@Data
public class Paper {
    private String title;
    private String introduction;
    private Content content;
    private String conclusion;
}
```

其中正文类如下:

```java
@Data
public class Content {
    private String text;
    private String picture;

    @SneakyThrows
    public Content(String text, String picture) {
        this.text = text;
        this.picture = picture;
        //很耗时的操作
        Thread.sleep(1000);
    }
}
```

我们用 Client 模拟远程操作:

```java
public class Client {

    //模拟远程查询操作
    public static String queryTitle() {
        return "气功治愈癌症的研究";
    }
}
```

写出二篇论文:

```java
        Paper firstPaper = new Paper();
        firstPaper.setTitle(Client.queryTitle());
        firstPaper.setIntroduction("青蛙是用腿叫的");
        firstPaper.setContent(new Content("青蛙本来叫的好好的, " +
                "切了腿就不叫了, 所以青蛙是用腿叫的",
                "a.jpg"));
        firstPaper.setConclusion("气功对于治愈癌症确实有效果");

        System.out.println("第一篇论文是 " + firstPaper);

        //将图片转个 90 度变成新的论文
        Paper secondPaper = new Paper();
        secondPaper.setTitle(Client.queryTitle());
        secondPaper.setIntroduction("青蛙是用腿叫的");
        secondPaper.setContent(new Content("青蛙本来叫的好好的, " +
                "切了腿就不叫了, 所以青蛙是用腿叫的",
                "b.jpg"));
        secondPaper.setConclusion("气功对于治愈癌症确实有效果");

        System.out.println("第二篇论文是 " + secondPaper);
```

## 问题分析

我们发现第一篇是没办法, 得写, 第二篇不用一个个字的重新打, 思路也不用重新整理, 我们单纯替换一下照片就是一个新的论文了, 省去了远程调用的 IO 资源占用和耗时

## 初步解决

我们为 paper 定义一个 clone 方法:

```java
@Data
public class Paper implements Cloneable {
    private String title;
    private String introduction;
    private Content content;
    private String conclusion;

    @Override
    public Paper clone() {
        Paper clonePaper = new Paper();
        clonePaper.setTitle(this.title);
        clonePaper.setIntroduction(this.introduction);
        clonePaper.setContent(this.content);
        clonePaper.setConclusion(this.conclusion);
        return clonePaper;
    }
}
```

我们创建第二个代码可以这么写:

```java
Paper firstPaper = new Paper();
firstPaper.setTitle(Client.queryTitle());
firstPaper.setIntroduction("青蛙是用腿叫的");
firstPaper.setContent(new Content("青蛙本来叫的好好的, " +
        "切了腿就不叫了, 所以青蛙是用腿叫的",
        "a.jpg"));
firstPaper.setConclusion("气功对于治愈癌症确实有效果");

System.out.println("第一篇论文是 " + firstPaper);

Paper secondPaper = firstPaper.clone();
secondPaper.getContent().setPicture("b.jpg");
System.out.println("第一篇论文是 " + firstPaper);
System.out.println("第二篇论文是 " + secondPaper);
```

看似没什么问题, 可是我们输出的时候变成了这样:

```
第一篇论文是 Paper(title=气功治愈癌症的研究, introduction=青蛙是用腿叫的, content=Content(text=青蛙本来叫的好好的, 切了腿就不叫了, 所以青蛙是用腿叫的, picture=a.jpg), conclusion=气功对于治愈癌症确实有效果)
第一篇论文是 Paper(title=气功治愈癌症的研究, introduction=青蛙是用腿叫的, content=Content(text=青蛙本来叫的好好的, 切了腿就不叫了, 所以青蛙是用腿叫的, picture=b.jpg), conclusion=气功对于治愈癌症确实有效果)
第二篇论文是 Paper(title=气功治愈癌症的研究, introduction=青蛙是用腿叫的, content=Content(text=青蛙本来叫的好好的, 切了腿就不叫了, 所以青蛙是用腿叫的, picture=b.jpg), conclusion=气功对于治愈癌症确实有效果)
```

因为我们是浅复制, 我们复制后对象的属性变了, 原来的对象的属性也跟着变了, 这肯定不是我们希望的

## 原型模式

我们可以用序列化来实现深度复制:

```
public class BeanUtil {

    public static <T> T deepClone(T source, Class<T> tClass) {
        return GsonUtil.convert(GsonUtil.toJson(source), tClass);
    }
}
```

其中的 GsonUtil 如下:

```java
public class GsonUtil {

    private static final Gson GSON = new GsonBuilder()
            .enableComplexMapKeySerialization()
            .create();

    private GsonUtil() {
    }

    public static <T> T convert(String jsonStr, Class<T> classOfT) {
        return GSON.fromJson(jsonStr, classOfT);
    }

    public static String toJson(Object target) {
        return GSON.toJson(target);
    }
}
```

## 课后作业

1. 使用深度复制完成 Paper 对象的复制
2. 一个报销单的一行如果由一个部门报销就直接插入库中, 由三个部门分摊报销就需要复制报销单的其他信息, 把原有的id后面加上1,2,3 这样, 请用原型模式实现:

```java
@Data
@EqualsAndHashCode(callSuper = false)
@Accessors(chain = true)
@TableName("m_c_expense_ReportEntry")
public class MCExpenseReportEntry implements Serializable {

    private static final long serialVersionUID = 1L;

    @TableField("ExpenseTypeName")
    private String expenseTypeName;

    @TableField("CurrencyCode")
    private String currencyCode;

    @TableField("ApprovedAmount")
    private BigDecimal approvedAmount;

    @TableField("LocationCountry")
    private String locationCountry;

    @TableField("LocationName")
    private String locationName;

    @TableField("BusinessPurpose")
    private String businessPurpose;

    @TableField("JoinNum")
    private String joinNum;

    @TableField("AbnormalProblems")
    private String abnormalProblems;

    @TableField("ProblemsDescription")
    private String problemsDescription;

    @TableField("RecheckOpinion")
    private String recheckOpinion;

    @TableField("SecondInstance")
    private String secondInstance;

    @TableField("BearToPayDepartmentCode")
    private String bearToPayDepartmentCode;

    @TableField("BearToPayDepartment")
    private String bearToPayDepartment;

    @TableField("ReportID")
    private String reportID;

    @TableField("TransactionDate")
    private LocalDateTime transactionDate;

    @TableField("Report_Entry_Id")
    private String reportEntryId;

    private String isTax;

    private String invoiceType;

    @TableField("Percentage")
    private String percentage;

    @TableField("LastModifiedDate")
    private String lastModifiedDate;

    @TableField("financial_approval_date")
    private LocalDateTime financialApprovalDate;
}
```





















