在 Java 8 中，引入了 Lambda 表达式和函数式接口，使得 Java 语言更加函数式，其中包括了箭头 foreach 语法。

箭头 foreach 语法是一种简化的循环语法，用于遍历集合类（如 List、Set、Map 等）中的元素。箭头 foreach 语法的基本语法如下：

```java
collection.forEach(element -> {
    // 处理 element 的逻辑
});
```

其中，`collection` 是要遍历的集合类对象，`element` 是集合中的每个元素，在箭头后面的代码块中，可以对 `element` 进行一些处理，例如打印、过滤、转换等等。

下面是一些使用箭头 foreach 语法的例子：

遍历 List：

```java
List<String> list = Arrays.asList("Java", "Python", "JavaScript");

list.forEach(str -> {
    System.out.println(str);
});
```

遍历 Set：

```java
Set<Integer> set = new HashSet<>(Arrays.asList(1, 2, 3, 4, 5));

set.forEach(num -> {
    System.out.println(num);
});
```

遍历 Map：

```java
Map<String, Integer> map = new HashMap<>();
map.put("Java", 1);
map.put("Python", 2);
map.put("JavaScript", 3);

map.forEach((key, value) -> {
    System.out.println(key + ": " + value);
});
```

需要注意的是，箭头 foreach 语法只能用于遍历集合类中的元素，不能用于数组或基本数据类型的遍历。此外，如果要在遍历中修改集合中的元素，需要使用迭代器或其他方式进行修改，否则会抛出 `ConcurrentModificationException` 异常。