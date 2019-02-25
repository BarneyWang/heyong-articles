# Guide To Java 8 Optional



## **1. Overview**

在这篇文章里吗，我们将要展示Java 8 采用的新的Optional Class。

这个类的作用是为可选择的值来代替使用null的应用提供类型级别的解决方案。

获得关于为什么我们要关心可选的class一个更深的理解，看一下官方oracle的[文档](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html)

Optional是java.util包的一部分，所以我们需要引入这个包

``` java
import java.util.Optional;
```



## **2. Creating Optional Objects**

这里有几种创建Optional对象的方法，去创建一个空的Object对象：

``` java
@Test
public void whenCreatesEmptyOptional_thenCorrect(){
    Optional<String> empty = Optional.empty();
    assertFalse(empty.isPresent());
}
```

 *isPresent* Api是用来检查是否有一个值在Optonal对象的里边。当切仅当我们查u你更加爱你了一个非空值的Optinal对象值才被会呈现。我们在下一个段落接着看*isPresent* Api。

我们也可以用静态 *of*  api来创建Optional对象。

``` java
@Test
public void givenNonNull_whenCreatesNonNullable_thenCorrect() {
    String name = "baeldung";
    Optional.of(name);
}
```

这个值可以处理：

``` java
@Test
public void givenNonNull_whenCreatesOptional_thenCorrect() {
    String name = "baeldung";
    Optional<String> opt = Optional.of(name);
    assertEquals("Optional[baeldung]", opt.toString());
}
```

然而， *of*方法的参数传递不可以为null，否则，回抛出*NullPointException*:

```java
@Test(expected = NullPointerException.class)
public void givenNull_whenThrowsErrorOnCreate_thenCorrect() {
    String name = null;
    Optional<String> opt = Optional.of(name);
}
```

但是，如果我们期望传入的参数有一些null值，我们可以使用*ofNullable* API：

``` java
@Test
public void givenNonNull_whenCreatesNullable_thenCorrect() {
    String name = "baeldung";
    Optional<String> opt = Optional.ofNullable(name);
    assertEquals("Optional[baeldung]", opt.toString());
}
```

这样，如果我们传递进了一个null的应用，他不会抛出一个异常相反会返回一个空的Optional对象好像我们使用*Optional.empty* API创建它一样。

``` java
@Test
public void givenNull_whenCreatesNullable_thenCorrect() {
    String name = null;
    Optional<String> opt = Optional.ofNullable(name);
    assertEquals("Optional.empty", opt.toString());
}
```

## **3. Checking Value Presence: isPresent() and isEmpty()**

当我们来自一个方法返回或者我们自己创建了一个*Optional*对象的时候，我们可以使用*isPresent()* API来检查这个值是否存在：

``` java
@Test
public void givenOptional_whenIsPresentWorks_thenCorrect() {
    Optional<String> opt = Optional.of("Baeldung");
    assertTrue(opt.isPresent());
 
    opt = Optional.ofNullable(null);
    assertFalse(opt.isPresent());
}
```

该API会返回true 当且仅当被包装的值不是null的时候。

同样，从java 11 开始，我们可以使用*isEmpty*来做相反的事情：

``` java
@Test
public void givenAnEmptyOptional_thenIsEmptyBehavesAsExpected() {
    Optional<String> opt = Optional.of("Baeldung");
    assertFalse(opt.isEmpty());
 
    opt = Optional.ofNullable(null);
    assertTrue(opt.isEmpty());
}
```

*isEmpty*方法返回*true* 如果被包装的值是null。反之，返回*false* 

## **4. Conditional Action With ifPresent()** 使用isPresent()的条件操作？

*ifPresent* 让我们可以运行一下代码在包装的值上如果值被发现不是*non-null* 值的话。在Optional出现之前，我们是这个么干的：

``` java
if(name != null){
    System.out.println(name.length);
}
```

这段代码在继续执行代码之前检查name变量是*null*还是不是。这种途径是冗长的和不是唯一的问题。这种方法固有的很多潜在的bug。

在习惯了使用这种发放以后。人们很容易忘记在代码的某些部分执行null 检查。

如果一个null值进入了那段代码，这可能会导致运行时抛出一个*NullPointerException*。如果一个程序由于输入的问题失败了，那么它通常是编程实践不佳的结果。

*Optinal*可以明确地处理可空值，以强制执行良好的编程实践。让我们现在看一下上面的代码如何被Java 8 重构。

在典型的函数式编程的风格里，我们可以执行对实际存在的对象进行操作：

``` java
@Test
public void givenOptional_whenIfPresentWorks_thenCorrect() {
    Optional<String> opt = Optional.of("baeldung");
 
    opt.ifPresent(name -> System.out.println(name.length()));
}
```

在上面这段代码例子中，我们使用的两行代码来代替第一个例子中起作用的五行代码。一行把一个对象包装进入*Optinal* 对象中，接下来隐形的执行验证以及执行代码。

## **5. Default Value With orElse** 默认的使用orElse的值

*orElse* API 被用纠正被包装在一个*Optional*实例中的值。它需要一个参数来充当默认值,在*orElse*中，如果被包装的值不存在着返回orElse给出的参数，如果存在就返回被包装的值：

``` java
@Test
public void whenOrElseWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElse("john");
    assertEquals("john", name);
}
```

## **6. Default Value With orElseGet**

*orElseGet* API与*orElse*很像。然而它需要一个被调用和返回调用的值的supplier函数接口，来代替如果一个*Optional*值不存在的话返回一个值



``` java
@Test
public void whenOrElseGetWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElseGet(() -> "john");
    assertEquals("john", name);
}
```

## **7. Difference Between orElse and orElseGet**

对于很多新使用*Optional* 或者Java 8的编程者来说。*orElse* 与*orElseGet*的区别不是很明确.事实如此，这两个API给人的印象是在功能性上的相互重叠。

然而，两者之间存在微妙但非常重要的区别，如果不能很好地理解，它们会严重影响代码的性能。

让我们在一个测试类里边来创建一个没有参数和返回一个默认值的叫*getMyDefault*方法，

```java
public String getMyDefault() {
    System.out.println("Getting Default Value");
    return "Default Value";
}
```

我们创建两个test，观察他们的副作用，已确定他们不同点和相同点。

``` java
@Test
public void whenOrElseGetAndOrElseOverlap_thenCorrect() {
    String text;
    System.out.println("Using orElseGet:");
    String defaultText = 
      Optional.ofNullable(text).orElseGet(this::getMyDefault);
    assertEquals("Default Value", defaultText);
 
    System.out.println("Using orElse:");
    defaultText = Optional.ofNullable(text).orElse(getMyDefault());
    assertEquals("Default Value", defaultText);
}
```

在上面的例子里，我们在一个*Optional*对象里边包装了一个null值，我们意图获取使用两种途径来获取被包装的值。作用如下：

```
Using orElseGet:
Getting default value...
Using orElse:
Getting default value...
```

*getMyDefault*方法在每一个case里边都被调用了。如果被包装的值没有的话，这俩方法都以完全相同的方式工作。

让我们来跑另外一个test，其中值存在，理想化。甚至默认值都不被创建。

```java
@Test
public void whenOrElseGetAndOrElseDiffer_thenCorrect() {
    String text = "Text present";
    System.out.println("Using orElseGet:");
    String defaultText 
      = Optional.ofNullable(text).orElseGet(this::getMyDefault);
    assertEquals("Text present", defaultText);
 
    System.out.println("Using orElse:");
    defaultText = Optional.ofNullable(text).orElse(getMyDefault());
    assertEquals("Text present", defaultText);
}
```

在上面的例子中，我们不再包装一个null值，其他的代码是一样的。现在我们来看一看跑这个代码的效果。

```
Using orElseGet:
Using orElse:
Getting default value...
```

注意到，当我们使用*orElseGet*去取回被包装的值的时候，由于（因为）包含的值存在，*getMyDefault*甚至没有被调用。

然而，当使用*orElse*时，无论被包装的值出现与否，默认是的对象还是被创建了，所以在这个例子里，我们创建了一个永远不会被使用的冗余对象。

在这样的简单的例子里，创建默认对象没有太大的成本，因为JVM知道如何处理这样的问题。然而，当一个如同*getMyDefault*的方法不得不被WebService调用，甚至查询一个数据库时候，这个花销是将非常明显的。



## **8. Exceptions with orElseThrow**

*orElseThrow* API来从*orElseGet* 和*orElse*中得出的，添加了一个新的途径去处理未出现的值。它抛出Exception来代替当被包装的值没有出现而返回的值。

```java
@Test(expected = IllegalArgumentException.class)
public void whenOrElseThrowWorks_thenCorrect() {
    String nullName = null;
    String name = Optional.ofNullable(nullName).orElseThrow(
      IllegalArgumentException::new);
}
```

Java 8的方法引用在这里派上用场，来传递Exception的构造函数

## **9. Returning Value with get()**

取回被包装的值的最终途径是*get()*API:

``` java
@Test
public void givenOptional_whenGetsValue_thenCorrect() {
    Optional<String> opt = Optional.of("baeldung");
    String name = opt.get();
 
    assertEquals("baeldung", name);
}
```

然而，不同于上面的三个方法，这个get API只能在被包装的值不是null的时候返回一个值，反之，它会抛出一个*NoSuchElementException*：

``` java
@Test(expected = NoSuchElementException.class)
public void givenOptionalWithNull_whenGetThrowsException_thenCorrect() {
    Optional<String> opt = Optional.ofNullable(null);
    String name = opt.get();
}
```

这是*get* API的重大缺陷。理想情况下，*Optional* 可以帮助我们忽略这样的意料之外的exception。因此，这个途径违反了*Optional* 的目标，可能在未来的版本中被废弃。

因此，建议使用其他能够使我们能够准备并明确处理null情况的变体。

## **10. Conditional Return with filter()**

*filter()* 被用来在被包装的值上跑一下内敛测试（inline test）。他用一个 *predicate* 作为参数，返回返回一个*Optional* 对象。如果被包装的值通过了 *predicate* 的测试，那么就会按照原样返回*Optional*。

然而，如果 *predicate*返回false，那么会返回一个空的*Optional*：

```java
@Test
public void whenOptionalFilterWorks_thenCorrect() {
    Integer year = 2016;
    Optional<Integer> yearOptional = Optional.of(year);
    boolean is2016 = yearOptional.filter(y -> y == 2016).isPresent();
    assertTrue(is2016);
    boolean is2017 = yearOptional.filter(y -> y == 2017).isPresent();
    assertFalse(is2017);
}
```

*filter* API 一般地用来基于预先定义的规则来拒绝被包装的值。你可以用它来拒绝一个格式错误的email或者一个不够强壮的密码。

让我们来看另外一个有意义的例子。让我们说我们想要卖一个modem（调制解调器），我们只关心他的价格。我从一个确切的网站上收到了modem的价格推送通知并且用对象来储存它。

```java
public class Modem {
    private Double price;
 
    public Modem(Double price) {
        this.price = price;
    }
    //standard getters and setters
}
```

然后我们来搞一下代码，唯一的用途就是检查modem的价格是否符合我们的预算范围。我们想要花10到15刀买他。让我们来看一下不用Optional的代码：

```java
public boolean priceIsInRange1(Modem modem) {
    boolean isInRange = false;
 
    if (modem != null && modem.getPrice() != null
      && (modem.getPrice() >= 10
        && modem.getPrice() <= 15)) {
 
        isInRange = true;
    }
    return isInRange;
}
```

注意的是 我们必须写多少代码来实现这个功能，特别是在*if*条件里。if条件的唯一对应用程序重要的部分就是最后的价格范围检查：其他检查都是防御性的：

``` java
@Test
public void whenFiltersWithoutOptional_thenCorrect() {
    assertTrue(priceIsInRange1(new Modem(10.0)));
    assertFalse(priceIsInRange1(new Modem(9.9)));
    assertFalse(priceIsInRange1(new Modem(null)));
    assertFalse(priceIsInRange1(new Modem(15.5)));
    assertFalse(priceIsInRange1(null));
}
```

除此之外，在一整天没有获得任何编译时错误的情况下忘记了null检查。

让我们来看一个*Optional  fiter()* API的变种（variant）： 

``` java
public boolean priceIsInRange2(Modem modem2) {
     return Optional.ofNullable(modem2)
       .map(Modem::getPrice)
       .filter(p -> p >= 10)
       .filter(p -> p <= 15)
       .isPresent();
 }
```

*map* 方法只是用于把一个值转换成另一个值。记住这个操作不会改变原始值。

在我们的情况里，我们从*Model* class里边获得了一个price对象，我们将在下一个章节中详细介绍*map* API。

首先，如果一个空值被传递到了这个API，我们预计不会出现任何问题。

第二，在代码体里边我们写的唯一逻辑正是API名称描述的内容，价格区间检查。*Optional* 关注其他部分。

```java
@Test
public void whenFiltersWithOptional_thenCorrect() {
    assertTrue(priceIsInRange2(new Modem(10.0)));
    assertFalse(priceIsInRange2(new Modem(9.9)));
    assertFalse(priceIsInRange2(new Modem(null)));
    assertFalse(priceIsInRange2(new Modem(15.5)));
    assertFalse(priceIsInRange2(null));
}
```

之前的API承诺去检查价格区间，但是它不得不做更多来防御它固有的脆弱性。因此，*filter* API可能被用来替换不重要的*if*声明来拒绝不想要的值。

## **11. Transforming Value with map()** 用map()来转换值

在前面的章节里，我们看到了怎么通过一个*filter*来拒绝或者接受一个值。相似的语法可以通过*map* API来转换*Optional*的值：

``` java
@Test
public void givenOptional_whenMapWorks_thenCorrect() {
    List<String> companyNames = Arrays.asList(
      "paypal", "oracle", "", "microsoft", "", "apple");
    Optional<List<String>> listOptional = Optional.of(companyNames);
 
    int size = listOptional
      .map(List::size)
      .orElse(0);
    assertEquals(6, size);
}
```

在这个例子里，我们包装了一个strings list在一个*Optional*对象里，使用它的*map*API在包含的list里边执行一个动作（perform an antion on the contained list）。我们执行的操作就是取得list的大小。

*map* API返回包含在*Optional*里边的计算结果。然后我们必须在返回的*Optional*上调用适当的api来获取它的值。

值得注意的是*filter* API只是执行对值的的一个检查和返回一个boolean。在另一方面，*map* API获得了存在的值，使用这个值执行一个计算并且返回了包含在*Optional*对象里边计算的结果。

``` java
@Test
public void givenOptional_whenMapWorks_thenCorrect2() {
    String name = "baeldung";
    Optional<String> nameOptional = Optional.of(name);
    int len = nameOptional
     .map(String::length())
     .orElse(0);
    assertEquals(8, len);
}
```

我们可以把*map*和*filter*连在一起来做一些更有强大的事。

让我们假设我们想要去检查一个用户输入的密码的正确性；我们可以使用*map*转换器来清理密码并使用一个*filter*来检查它的正确性。

```
@Test
public void givenOptional_whenMapWorksWithFilter_thenCorrect() {
    String password = " password ";
    Optional<String> passOpt = Optional.of(password);
    boolean correctPassword = passOpt.filter(
      pass -> pass.equals("password")).isPresent();
    assertFalse(correctPassword);
 
    correctPassword = passOpt
      .map(String::trim)
      .filter(pass -> pass.equals("password"))
      .isPresent();
    assertTrue(correctPassword);
}
```

如你们所看到的是，没有第一次清理输入，它会被过滤掉，然而用户可能理所当然的认为前空格和后面的空格都工程了输入，所以在过滤掉正确密码之前，我们通过*map*方法来把脏密码转换成干净的密码。

## **12. Transforming Value with flatMap()**

如同*map* API，我们也有*flatMap*来作为转换值的替代方法，不同点在于 *map*只能在转换展开的值而*flatMap*可以获得包装的值，在转换它之前先展开值。

先前，我们已经创建了简单的String和Integer对象，封装进了一个*Optional*实例里。然而，通常，我们要从一个复杂的对象的访问器来取货这些对象。

为了清楚的了解差异（To get a clearer picture of the difference,），让我们看一下Person对象，它有一个人的详细信息，如姓名，年龄和密码：

``` java
public class Person {
    private String name;
    private int age;
    private String password;
 
    public Optional<String> getName() {
        return Optional.ofNullable(name);
    }
 
    public Optional<Integer> getAge() {
        return Optional.ofNullable(age);
    }
 
    public Optional<String> getPassword() {
        return Optional.ofNullable(password);
    }
 
    // normal constructors and setters
}
```

我们将要通常穿件一个这样的对象并且把它封装在一个*Optional*对象里如果我们对String做的那样。另外(Alternatively),它可以被另一个API的调用返回给我们：

``` java
Person person = new Person("john", 26);
Optional<Person> personOptional = Optional.of(person);
```

现在值得注意的是我们封装了一个*Person*的对象，它包含一些相关的*Optional*实例：

``` java
@Test
public void givenOptional_whenFlatMapWorks_thenCorrect2() {
    Person person = new Person("john", 26);
    Optional<Person> personOptional = Optional.of(person);
 
    Optional<Optional<String>> nameOptionalWrapper  
      = personOptional.map(Person::getName);
    Optional<String> nameOptional  
      = nameOptionalWrapper.orElseThrow(IllegalArgumentException::new);
    String name1 = nameOptional.orElse("");
    assertEquals("john", name1);
 
    String name = personOptional
      .flatMap(Person::getName)
      .orElse("");
    assertEquals("john", name);
}
```

目前为止，我们正在试着检索*Persion*对象的name属性以执行一个断言（assertion）

注意到在第三个声明里我们怎么使用*map*API来实现的，然后注意我们在之后使用*flatMap*API做的同样的事。

*Person::getName* 方法与之前清理密码那一章节我们使用的*String::trim*相似。

不同的是 *getName()* 返回一个*Optional* 而不是 作出*trim() *操作返回的一个String。这与将* *map* 转换结果包装在  *Optional*对象中的事实相结合，导致嵌套的*Optional*，当使用*map*API的时候。因此，我们需要添加一个额外的调用来检索被转换之前的值。这方法里 ，the*Optional*包装会被删除。这个操作在使用*flatMap*时候会隐式地被执行。

## 13. JDK 9 *Optional* API

随着Java 9被推出，跟过新方法将会被添加到*Optional*API:

+ *or()* 方法提供了创建可选的supplier
+ *ifPresentOrElse*方法允许当*Optional*是出现的时候执行一个动作，当*Optional*不在的时候执行另一个
+ *stream()* 方法用于转化一个*Optional*成为一个*Stream*

[这里进一步阅读完全的文章](https://www.baeldung.com/java-9-optional)

## **14. Conclusion** 结论

在这篇文章流，我们覆盖了java8 *Optional* 类的大部分重要的功能。

同时我们也简要地探索了一下我们为什么要使用*Optional*来代替显示地null检查和输入验证的理由。

最后,我们触碰了一下微小但是重要的*orElse*和*orElseGet*的区别。

关于这个话题[这里有一些进一步阅读的好文章](https://www.baeldung.com/java-filter-stream-of-optional)





***

原文地址:https://www.baeldung.com/java-optional

