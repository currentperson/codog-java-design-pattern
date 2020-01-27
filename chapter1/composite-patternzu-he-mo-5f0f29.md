## 目的

对树形结构的中间节点和叶子节点对客户端保持一致的处理

## 例子代码

最近全国都在新型冠状病毒引起的肺炎的阴影之下, 从年前隐隐约约听说武汉有个人得了非典, 到截止写文章的这个时候, 根据@今日头条的数据来看, 目前全国确诊 2823 例, 死亡 81 例, 连我们这个 10086 线的城市都有确诊案例, 这里面应该还有很多农村或者欠发达地区的一个统计盲点, 局势是非常严峻了, 但是现在应该还没有能够治愈的药物, 这次我也是领悟到了, 平安健康就是最好的, 希望大家都能够平安健康.

大家目前肯定是希望能够进行消毒操作, 下面我们模拟给湖北省消毒的一段代码:

我们先定义一个省的结构:

```java
@Data
public class Province {

    private String name;

    private List<City> cities = new ArrayList<>();

    public Province(String name) {
        this.name = name;
    }

    public void killVirus() {
        cities.forEach(City::killVirus);
    }

    public void addCity(City city) {
        cities.add(city);
    }

    public void removeCity(City city) {
        cities.remove(city);
    }
}
```

省是由市组成的:

```java
@Data
public class City {

    private String name;

    private List<Mall> malls = new ArrayList<>();
    private List<School> schools = new ArrayList<>();

    public City(String name) {
        this.name = name;
    }

    public void killVirus() {
        malls.forEach(Mall::killVirus);
        schools.forEach(School::killVirus);
    }

    public void addMall(Mall mall) {
        malls.add(mall);
    }

    public void removeMall(Mall mall) {
        malls.remove(mall);
    }

    public void addSchool(School school) {
        schools.add(school);
    }

    public void removeSchool(School school) {
        schools.remove(school);
    }
}
```

市里面有商场和学校是消毒的重点:

商场:

```java
@Data
public class Mall {

    private String name;

    public Mall(String name) {
        this.name = name;
    }

    public void killVirus() {
        System.out.println("给" + name + "商场区域消毒");
    }

}
```

学校:

```java
@Data
public class School {

    private String name;

    public School(String name) {
        this.name = name;
    }

    public void killVirus() {
        System.out.println("给" + name + "学校区域消毒");
    }
}
```

我们尝试进行消毒:

```java
Province province = new Province("湖北省");
City wuhan = new City("武汉市");
wuhan.addMall(new Mall("华南海鲜市场"));
wuhan.addSchool(new School("武汉一中"));
province.addCity(wuhan);
City huanggang = new City("黄冈市");
huanggang.addMall(new Mall("家乐福"));
huanggang.addSchool(new School("黄冈一中"));
province.addCity(huanggang);
province.killVirus();
```

## 问题分析

这个里面有一个很严重的问题, 就是如果我们再加一个医院也作为消毒的场所, city 类就需要改, 再加一个办公楼也作为消毒场所, city 类还需要改, city 类累了

## 组合模式

我们先定义一个节点间共有的接口:

```java
public interface Place {
    void add(Place place);
    void remove(Place place);
    void killVirus();
}
```

定义通用的中间节点的实现:

```java
public abstract class AbstractCompositePlace implements Place {

    private List<Place> list = new ArrayList<>();

    @Override
    public void add(Place place) {
        list.add(place);
    }

    @Override
    public void remove(Place place) {
        list.remove(place);
    }

    @Override
    public void killVirus() {
        list.forEach(Place::killVirus);
    }
}
```

定义通用的叶子节点的实现:

```java
public abstract class AbstractLeafPlace implements Place {

    @Override
    public void add(Place place) {

    }

    @Override
    public void remove(Place place) {

    }
}
```

再来定义我们之前的几个类:

```java
@Data
public class Province extends AbstractCompositePlace {

    private String name;

    public Province(String name) {
        this.name = name;
    }
}
```



```java
@Data
public class City extends AbstractCompositePlace {
    private String name;

    public City(String name) {
        this.name = name;
    }
}
```



```java
@Data
public class Mall extends AbstractLeafPlace {
    private String name;

    public Mall(String name) {
        this.name = name;
    }

    @Override
    public void killVirus() {
        System.out.println("给" + name + "商场区域消毒");
    }
}
```



```java
@Data
public class School extends AbstractLeafPlace {
    private String name;

    public School(String name) {
        this.name = name;
    }

    @Override
    public void killVirus() {
        System.out.println("给" + name + "学校区域消毒");
    }
}
```

然后我们的客户端代码如下:

```java
Place province = new Province("湖北省");
Place wuhan = new City("武汉市");
wuhan.add(new Mall("华南海鲜市场"));
wuhan.add(new School("武汉一中"));
province.add(wuhan);
Place huanggang = new City("黄冈市");
huanggang.add(new Mall("家乐福"));
huanggang.add(new School("黄冈一中"));
province.add(huanggang);
province.killVirus();
```

输出如下:

```java
给华南海鲜市场商场区域消毒
给武汉一中学校区域消毒
给家乐福商场区域消毒
给黄冈一中学校区域消毒
```

类图如下:![](/assets/2020012701.png)最近老妈作为一个乡村医生, 每天都在挨家挨户的量体温, 排查外来人口, 去镇上开会汇报二个村子的情况, 还带个简单的口罩顶着感染的风险去给乡亲们打针输液, 向医疗工作者致敬! 

