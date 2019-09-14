# Builder Pattern\(建造者模式\)

## 目的

1. 减少构造函数的数量, 去除参数过多的构造函数, 参数过多会引起可读性和易用性下降
2. 将某个构造耗时的部分异步构造, 等到真正用到的时候再完全构造出来
3. 某个复杂类的某个部分的构造实现和各个部分的构造顺序分离开, 使得各个部分的既能按照一定的顺序构建, 又能对调用方屏蔽顺序细节

## 生产实践

### 使用 Builder 插件

插件地址: [https://plugins.jetbrains.com/plugin/6585-builder-generator/](https://plugins.jetbrains.com/plugin/6585-builder-generator/)



### 使用 Lombok 插件

插件地址: [https://plugins.jetbrains.com/plugin/6317-lombok/](https://plugins.jetbrains.com/plugin/6317-lombok/)

