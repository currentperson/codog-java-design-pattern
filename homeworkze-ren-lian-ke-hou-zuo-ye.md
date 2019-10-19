# 责任链课后作业

1. 结合 bean 声明学习在 Spring boot 中优雅使用责任链模式
2. 学习使用 Apache Commons Chain 包
3. 作为一个银行开发人员, 我们有个支用请求, 需要验证支用币种是否是CNY\(人民币\),用户是否在黑名单,是否是实名认证的用户,支用的产品是否可用,支用金额是否充足等, 请使用责任链模式完成

提供了如下入参:

```java
@Data
public class LendRequest {

    //支用金额
    private String loanAmt;

    //支用币种
    private String currency;

    //支用产品
    private String pdCode;

    //用户唯一标识
    private String userId;
}
```

提供的接口如下:

```
public class Client {

    //是否不在黑名单
    public static boolean isNotInBlack(String userId) {
        return true;
    }

    //是否是实名认证用户
    public static boolean isRealUser(String userId) {
        return true;
    }

    //是否产品可用
    public static boolean isPdUsable(String pdCode) {
        return true;
    }

    //是否用户额度充足
    public static boolean isHasSoMuchMoney(String userId, String loanAmt) {
        return false;
    }
}
```





