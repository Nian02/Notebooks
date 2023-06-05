```java
// JDK7:
ArrayList<String> list = new ArrayList<>();// 泛型：限定集合中存储数据的类型
System.out.println(list);// []
```

## ArrayList 成员方法

-  `boolean add(E e)` 添加元素，返回值表示是否添加成功（永远return true）
-  `boolean remove(E e)` 删除指定元素，返回值表示是否删除成功
-  `E remove(int index)` 删除指定索引的元素，返回被删除元素
-  `E set(int index, E e)` 修改指定索引的元素，返回原来的元素
-  `E get(int index)` 获取指定索引的元素
-  `int size()` 集合的长度，也就是集合中的元素个数
