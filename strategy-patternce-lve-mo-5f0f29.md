## 目的

做一件事情有不同的实现方式, 可以将变化的部分和不变得部分剥离开, 去除大量的 if/else, 提供高扩展性

## 例子代码

比如我们要带妹吃鸡, 就要成为一个神枪手, 在各种枪战游戏中, 有各种不同的枪, 我们要根据实际情况\(比如射程\)的不同选择不同的枪进行射击, 我们可以假设没把枪的子弹数量都不富裕, 我们要用最合适的方法, 最少的子弹完成最强的伤害, 最终大吉大利, 今晚就可以想带妹子去哪里吃鸡都可以啦, 真的是哪里都可以哦

比如我们距离对手在一米内的时候使用平底锅\( 想我当时三级头三级甲, 手持 AKM, 满血满状态, 三级包里药包无数, 到了决赛圈被平底锅堵在墙角打死啦 \), 距离 100 米的时候使用冲锋枪, 超过1000 米使用狙击枪\(基本流程是开一枪没打中, 暴露位置, 被别人一狙打死\)

```java
/**
 * 面条式代码判断最强武器
 */
public class NoodlesKillProcessor {

    /**
     * 根据距离判断最好的武器击杀对手
     * @param distance
     */
    @BadSmell
    public static void killByDistance(int distance) {
        if(distance < 0) {
            throw new RuntimeException("距离咋还能是负数呢? 我以为只要我能 -20 cm 呢?");
        }

        if(distance >= 0 && distance < 1) {
            System.out.println("二步快速走过去");
            System.out.println("掏出平底锅呼死他");
        }
        if(distance >= 1 && distance < 10) {
            System.out.println("快速走过去");
            System.out.println("掏出手枪打死他");
        }
        if(distance >= 10 && distance < 100) {
            System.out.println("身体站直, 心态稳住");
            System.out.println("掏出冲锋枪打死他");
        }
        if(distance >= 100 && distance < 1000) {
            System.out.println("身体蹲下降低后坐力");
            System.out.println("掏出步枪");
            System.out.println("打开 3 倍镜");
            System.out.println("开枪射击");
        }
        if(distance > 1000) {
            System.out.println("趴在草丛里苟着");
            System.out.println("掏出狙击枪");
            System.out.println("打开 8 倍镜");
            System.out.println("开枪射击");
        }
    }
}
```





