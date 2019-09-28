## 目的

做一件事情遵循着一定的步骤, 但每一步的具体细节可能不同, 就像把东西放入冰箱需要三步, 冰箱门打开, 把东西放进去, 冰箱门关上, 区别只是把苹果放进去的时候一个手拿放进去, 把西瓜放进去需要二个手抬进去, 把大象放进去需要 ... ..\(我也不知道需要啥啦\). ,这时候可以把步骤这种不变的部分和每一步的具体细节这种经常变化的部分剥离开, 从而实现代码的可拓展性

## 例子代码

作为程序猿/媛, 我们都是 "工作 996, 生病 ICU"\([https://996.icu/\#/zh\_CN](https://996.icu/#/zh_CN)\), 还要一些同学都 "007" 啦, 听说加班加的老婆都跑啦, 囧, ~~鲁迅~~也说过\(好像不是周树人, 是鲁云还是马讯来着\): "年轻的时候不修福报, 什么时候修福报呢", 但是玩笑归玩笑, 好好工作固然很重要\(~~不好好工作, 老板怎么换车呢, 这句话我还真在工作中听过,,,~~\), 但是身体也是很重要的, 身体是最大的本钱, 有了身体不适的时候一定要去医院.

我们去看病的时候流程基本是固定的, 但是看每种不同的病的细节可能是不同, 比如码农几大职业病\( 没有科学数据, 乱说的, 但是小伙伴们也要注意预防呀\): 脱发, 青光眼, 颈椎病. 每种的治疗方法和所开的药等并不相同, 但是整体去看病的流程是相似的, 粗略分为挂号, 门诊, 拍片子, 买药这几个步骤, 这个时候刚学习了策略模式\(请看第三篇文档\)的同学就可以使用三种策略模式来完成这种模拟. 大概代码如下:

我们先定义一下策略模式的入参和出参:

入参:

```java
/**
 * 看病策略的入参
 */
@Data
public class SeeTheDoctorStrategyRequest extends AbstractStrategyRequest {
    /**
     * 年龄
     */
    private Integer age;
}
```

出参:

```java
/**
 * 返回药品类型
 */
@Getter
@AllArgsConstructor
public class SeeTheDoctorStrategyResponse extends AbstractStrategyResponse {
    /**
     * 药品类型
     */
    private MedicineType medicineType;
}
```

定义三个策略:

```java
/**
 * 去皮肤科看脱发
 */
public class SeeDermatologistStrategy implements 
            Strategy<SeeTheDoctorStrategyRequest, SeeTheDoctorStrategyResponse> {

    @Override
    public SeeTheDoctorStrategyResponse executeStrategy(
                        SeeTheDoctorStrategyRequest request) {
        System.out.println("挂皮肤科的号");
        System.out.println("拿了号在叫号大厅等待");
        System.out.println("皮肤科医生门诊");
        if(request.getAge() < Constant.BEST_AGE) {
            System.out.println("还年轻, 不用来复查了");
        } else {
            System.out.println("吃完药还得来复查");
        }
        System.out.println("初步诊断是肾虚, 买一些蚂蚁大力丸");
        return new SeeTheDoctorStrategyResponse(MedicineType.ANT_POWERFUL_PILL);
    }
}
```

```java
/**
 * 看眼科医生策略
 */
public class SeeOphthalmologistStrategy implements 
        Strategy<SeeTheDoctorStrategyRequest, SeeTheDoctorStrategyResponse> {

    @Override
    public SeeTheDoctorStrategyResponse executeStrategy(
                    SeeTheDoctorStrategyRequest request) {
        System.out.println("挂眼科的号");
        System.out.println("拿了号在叫号大厅等待");
        System.out.println("去眼科医生门诊");
        if(request.getAge() < Constant.BEST_AGE) {
            System.out.println("还年轻, 不用来复查了");
        } else {
            System.out.println("吃完药还得来复查");
        }
        System.out.println("买一些消炎药");
        return new SeeTheDoctorStrategyResponse(MedicineType.ANTICATARRHALS);
    }
}
```

```java
/**
 * 颈椎病看骨科医生
 */
public class SeeOrthopedicSurgeonStrategy implements 
        Strategy<SeeTheDoctorStrategyRequest, SeeTheDoctorStrategyResponse>  {

    @Override
    public SeeTheDoctorStrategyResponse executeStrategy(
                    SeeTheDoctorStrategyRequest request) {
        System.out.println("挂骨科的号");
        System.out.println("拿了号在叫号大厅等待");
        System.out.println("去骨科医生门诊");
        if(request.getAge() < Constant.BEST_AGE) {
            System.out.println("还年轻, 不用来复查了");
        } else {
            System.out.println("吃完药还得来复查");
        }
        System.out.println("买一些活血化瘀药");
        return new SeeTheDoctorStrategyResponse(MedicineType.BLOOD_CIRCULATION_DRUGS);
    }
}
```

## 问题分析

以上的代码已经比 if-else 清晰了多啦, 按照策略模式的讲解也可以写个 context 类来负责路由到相关的类, 可是这里仍然可以发现三个问题:

第一个问题就是每个策略里面有重复的代码, if-esle 每段都一样, 这些代码最好单独提取到父类中

第二个问题是这些个类的步骤相似的, 却将步骤分的不是很细, 本来是相同的挂号, 等待叫号, 检查, 买药这些步骤, 阅读者却要每个类里面的代码都要读一遍才知道这些类全部是这个步骤, 如果这每个方法都成千上万行, 那就很痛苦啦

第三个问题是这个类的职责不是很单一, 既负责了不同的看病细节的实现, 比如买什么药, 又负责了组织整个看病流程, 如果这二个维度有一个维度需要修改了, 那么就得修改这个类



## 初步尝试解决





