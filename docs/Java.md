## ==和equals
* == : 它的作用是判断两个对象的地址是不是相等。即，判断两个对象是不是同一个对象(基本数据类型==比较的是值，引用数据类型==比较的是内存地址)。
* equals() : 它的作用也是判断两个对象是否相等。但它一般有两种使用情况：
  * 情况 1：类没有覆盖 equals() 方法。则通过 equals() 比较该类的两个对象时，等价于通过“==”比较这两个对象。
  * 情况 2：类覆盖了 equals() 方法。一般，我们都覆盖 equals() 方法来比较两个对象的内容是否相等；若它们的内容相等，则返回 true (即，认为这两个对象相等)。

## 数组的特点
* 一致性：数组只能保存相同数据类型元素，元素的数据类型可以是任何相同的数据类型。
* 有序性：数组中的元素是有序的，通过下标访问。
* 不可变性：数组一旦初始化，则长度（数组中元素的个数）不可变。 数组是一种连续存储线性结构，元素类型相同，大小相等

## Java中的集合
* Map接口和Collection接口是所有集合框架的父接口
* Collection接口的子接口包括：Set接口和List接口
* Map接口的实现类主要有：HashMap、TreeMap、Hashtable、ConcurrentHashMap以及Properties等
* Set接口的实现类主要有：HashSet、TreeSet、LinkedHashSet等
* List接口的实现类主要有：ArrayList、LinkedList、Stack以及Vector等

## Set、List、Map的区别
### List
* 可以允许重复的对象。
* 可以插入多个null元素。
* 是一个有序容器，保持了每个元素的插入顺序，输出的顺序就是插入的顺序。

### Set
* 不允许重复对象
* 无序容器，你无法保证每个元素的存储顺序，TreeSet通过 Comparator 或者 Comparable 维护了一个排序顺序。
* 只允许一个 null 元素

### Map
* Map不是collection的子接口或者实现类。Map是一个接口。
* Map 的 每个 Entry 都持有两个对象，也就是一个键一个值，* Map 可能会持有相同的值对象但键对象必须是唯一的。
* Map 里你可以拥有随意个 null 值但最多只能有一个 null 键。

https://juejin.cn/post/6924472756046135304
