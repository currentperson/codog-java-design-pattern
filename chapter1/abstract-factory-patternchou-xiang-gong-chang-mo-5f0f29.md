## 目的

减少工厂方法情况下过多的工厂类, 将相关的类的创建使用同一个工厂方法

## 例子代码

最近有个考了 200 多分的同学冒充了清华的录取通知书, 家里杀鸡宰牛之后发现是假的, 听说其父亲被气得进了医院, 血压都比自己二个的分数高...

## 抽象工厂模式![](/assets/2020082100.png)

先定义一个 Offer:

![](/assets/202082101.png)

二个实现类:![](/assets/2020082102.png)和![](/assets/2020082103.png)再定义一个 Paper:

![](/assets/2020082104.png)

二个实现类:

![](/assets/2020082105.png)

和

![](/assets/2020082106.png)

定义一个印章:

![](/assets/2020082107.png)

和实现:

![](/assets/2020082108.png)和

![](/assets/2020082109.png)

定义一个工厂类:

![](/assets/2020082110.png)

实现:![](/assets/2020082111.png)和![](/assets/2020082112.png)使用:![](/assets/2020082113.png)输出:

```
假的 offer: 家里蹲大学
假的纸:绿色
萝卜印章: 高度院校印章
```


