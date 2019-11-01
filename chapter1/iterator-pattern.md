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
List<List<Student>> students = new ArrayList<>(16);
for (int i = 0; i < 10; i++) {
    students.add(Arrays.asList(
            new Student("朱坚强" + i, true),
            new Student("兰兰" + i, false),
            new Student("codog编程狗" + i, true)));
}
printAllName(students);
```

## 问题分析

这个代码我们认为有二处可能有隐患的地方: 一处是我们存储学生队列的这个结构\(list&lt;list&gt;\) 可能会改变, 另外一处是我们目前只支出从左到右访问, 如果有新的遍历要求我们就需要更新大量的上层代码, 我们写完了这个代码, 想要回家陪\(da\)老\(fei\)婆\(ji\), 结果走到半路上, 被叫着改了二个需求, 一个是把 list&lt;list&gt; 改成二维数据方式存储, 另外一个是需要队列中的女生单独报, 男生不报名了, 这时候我们说 printAllName 这个方法用了1万处了, 需要逐个改

## 迭代器模式

先定义一个学生队列类:

```java
@Data
@AllArgsConstructor
public class StudentLineUp {
    private List<List<Student>> students;
}
```

定义一个迭代器:

```java
public class LineUpIterator 
            implements Iterator<Student> {

    private StudentLineUp studentLineUp;
    private Integer totalCount;
    private Integer currentRow;
    private Integer currentCol;
    private Integer currentCount;

    public LineUpIterator(
                StudentLineUp studentLineUp) {
        this.studentLineUp = studentLineUp;
        this.totalCount = studentLineUp
                .getStudents()
                .stream().map(list -> list.size())
                .reduce(Integer::sum).get();
        this.currentRow = 0;
        this.currentCol = 0;
        this.currentCount = 0;
    }

    @Override
    public boolean hasNext() {
        return currentCount
                .compareTo(totalCount) < 0;
    }

    @Override
    public Student next() {
        if(!hasNext()) {
            throw new RuntimeException
                        ("没有下一个数据了");
        }
        if(studentLineUp.getStudents()
            .get(currentRow).size() <= currentCol) {
            currentRow++;
            currentCol = 0;
        }
        currentCount++;
        return studentLineUp.getStudents()
                .get(currentRow).get(currentCol++);
    }
}
```

实际使用:

```java
List<List<Student>> students = new ArrayList<>(16);
for (int i = 0; i < 10; i++) {
    students.add(Arrays.asList(
            new Student("朱坚强" + i, true),
            new Student("兰兰" + i, false),
            new Student("codog编程狗" + i, true)));
}
LineUpIterator lineUpIterator = 
new LineUpIterator(new StudentLineUp(students));
while (lineUpIterator.hasNext()) {
    System.out.println(lineUpIterator.next().getName());
}
```

## 更进一步

如果我们变化的维度减少一个, 比如说是这个遍历方式基本不变, 但是内部存储方式可能会变, 那我们可以对数据结构采用 Iterable 方式:

```java
@Data
@AllArgsConstructor
public class StudentLineUp implements Iterable<Student> {
    private List<List<Student>> students;

    @Override
    public Iterator<Student> iterator() {
        return new LineUpIterator(this);
    }
}
```

使用方式:

```java
List<List<Student>> students = new ArrayList<>(15);
for (int i = 0; i < 10; i++) {
    students.add(Arrays.asList(
            new Student("朱坚强" + i, true),
            new Student("兰兰" + i, false),
            new Student("codog编程狗" + i, true)));
}
for (Student student : new StudentLineUp(students)) {
    System.out.println(student.getName());
}
```



