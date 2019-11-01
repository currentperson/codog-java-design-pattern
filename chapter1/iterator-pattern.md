## 目的

不需要知道具体的数据存储方式对数据进行不同方式的遍历

## 例子代码

最近考答\(so\)题\(suo\) 获得了 10.24 元狗物券, 猛地想起抹茶妹妹不知道怎么样了, 兄弟告诉我 ~~不努力就不是我兄弟~~ 现在只要学习设计模式学的好, 女朋友就还在初中军训

![](/assets/2019110100.png)

比如我们有一个军训方队, 每天要点名, 这时候我们实现了如下代码:

先定义一个学生类:

```java
@Data
@AllArgsConstructor
public class Student {

    private String name;

    private Boolean sexIsBoy;
}
```

我们需要按照从左到右的顺序进行报名:

```java
public static final void printAllName(
        List<List<Student>> students) {
    for (List<Student> studentList : students) {
        for (Student student : studentList) {
            System.out.println(student.getName());
        }
    }
}
```

我们试验一下:

```java
List<List<Student>> students = new ArrayList<>(15);
for (int i = 0; i < 10; i++) {
    students.add(Arrays.asList(
            new Student("朱坚强" + i, true),
            new Student("兰兰" + i, false),
            new Student("codog编程狗" + i, true)));
}
printAllName(students);
```

## 问题分析





