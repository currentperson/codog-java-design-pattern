# 空对象课后作业

1. 了解使用 Nullable 接口和 Null 标记接口的显式空对象模式, 并比较二种方式的优劣
2. 思考空对象模式的缺点
3. 作为一个银行开发人员, 比如我们有一系列的还款计划列表, 我们要构造一个还款汇总类, 传入还款计划列表, 这个类能分别计算未还本金,未还利息,未还手续费三个项, 使用空对象模式实现

```java
@Getter
public class RepayPlanItem {
   //本期未还本金
   private BigDecimal unpayedPrice;
   //本期未还利率
   private BigDecimal unpayedRate;
   //本期未还手续费 
   private BigDecimal unpayedFee;      
}
```

汇总类如下:

```java
public class TotalRepayPlan {
    public TotalRepayPlan(List<RepayPlanItem> repayPlanItems) {
        //等待实现
    }
    
    public BigDecimal getTotalUnpayedPrice(){
        //等待实现
    }
    
    public BigDecimal getTotalUnpayedRate(){
        //等待实现
    }
 
    public BigDecimal getTotalUnpayedFee(){
        //等待实现
    }
}
```



