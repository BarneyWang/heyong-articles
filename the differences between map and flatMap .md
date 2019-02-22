##  **1.Overview**

 map()和flatmap()这个api都来自于函数式编程.在java 8中，你可以在Optional，Stream和 CompletableFuture发现这两个api（尽管名字略有不同）。
 Steam代表一个对象序列，而optionals 是代表值是存在或者缺席的类，在其他综合操作里，我们有map()和flatmap() 方法。
  尽管事实上这两个api都拥有一样的返回类型，他俩却是很不一样的。让我们通过分析一些简单的streams和optionals来解释一下这两个api的不同。

## **2. map and flatMap in Optionals**

Map()方法与Optional一起配合非常好 （如果函数需要返回我们需要的精确类型的话

```java
Optional<String> s = Optional.of("test");
assertEquals(Optional.of("TEST"), s.map(String::toUpperCase));
```

然而，在更复杂的场景里边我们可能要给予一个函数返回一个Optional类型，由于map()在内部实现一个额外的封装，在这样的cases里边使用map()可能会导致嵌套的结构。

让我们来看一额外的一个例子去更好的理解这种情形。

```
assertEquals(Optional.of(Optional.of("STRING")), 
  Optional
  .of("string")
  .map(s -> Optional.of("STRING")));
```

我们能看到的是，我们以一个Optional<Optional<String>>这样的嵌套的结构来结束这段情景。

虽然这段代码可以工作，但是它却是使用起来相当笨重，而且不能提供额外的null安全,所以如果使用一个flat结构会更好。

这就是flapMap()帮助我们去做的事情：

```
assertEquals(Optional.of("STRING"), Optional
  .of("string")
  .flatMap(s -> Optional.of("STRING")));
```

## **3. map and flatMap in Streams**

这两个方法在Optional类里边很相似。

map()方法在一个Stream实例里边封装了底层的序列，而flatmap则允许避免Stream<Stream<R>> 这种嵌套结构。

在接下来的例子里边，map()生产了一个由将输入值使用*toUpperCase()* 处理过的元素的结果组成的Stream。

```java
List<String> myList = Stream.of("a", "b")
  .map(String::toUpperCase)
  .collect(Collectors.toList());
  assertEquals(asList("A", "B"), myList);
```

map()在简单的场景中非常有作用，但是如果我们更复杂的类型比如list of lists作为输入的话。

我们来看下map()是如何工作的：

~~~java
List<List<String>> list = Arrays.asList(
  Arrays.asList("a"),
  Arrays.asList("b"));
System.out.println(list);
~~~

上面的代码片段打印出来 一个 list of lists 类型 

``` java
[[a],[b]]
```

现在我们用flatMap():

``` java
System.out.println(list
  .stream()
  .flatMap(Collection::stream)
  .collect(Collectors.toList()));
```

这样的一个代码片段的借口就会被铺平成[a,b]

flatMap()方法首先一个输入的Stream of Streams 铺平成 一个 Stream of Strings（想要了解铺平的原理，可以看看这篇[文章](https://www.baeldung.com/java-flatten-nested-collections)）。

## **4. Conclusion**

Java 8 给了我们使用原本在函数是编程里边使用的map()和flatMap()的机会。

我们能可在Streams和Optionals中调用他们。这些方法帮助我们拿到通过调用提供的映射函数的映射过的对象。