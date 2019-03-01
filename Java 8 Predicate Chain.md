# Java 8 Predicate Chain

## **1. Overview**

在这个快速教程里，我们将要讨论不同的方法去链接java 8中的谓词。

## **2. Basic Example**

首先，让我们来看一下如何用一个简单的谓词来过滤names链表：

``` java
@Test
public void whenFilterList_thenSuccess(){
   List<String> names = Arrays.asList("Adam", "Alexander", "John", "Tom");
   List<String> result = names.stream()
     .filter(name -> name.startsWith("A"))
     .collect(Collectors.toList());
    
   assertEquals(2, result.size());
   assertThat(result, contains("Adam","Alexander"));
}
```

在这里的例子里，我们使用谓词过滤我们的names链表，只留下以“A”开头的names：

``` java
name -> name.startsWith("A")
```

但是如果我们想要应用多个谓词呢？

## **3. Multiple Filters**

如果我们想要应用多个谓词，一个选项就是简单地链接多个过滤器：

``` java
@Test
public void whenFilterListWithMultipleFilters_thenSuccess(){
    List<String> result = names.stream()
      .filter(name -> name.startsWith("A"))
      .filter(name -> name.length() < 5)
      .collect(Collectors.toList());
 
    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

我们现在已经更新了我们的例子来过滤我们的链表，通过提取以“A”开头和长度小于5的names。

我们使用了2个过滤器—每一个是一个谓词。

## **4. Complex Predicate**

现在，代替使用多个过滤题，我们使用一个带有复杂谓词的过滤器：

``` java
@Test
public void whenFilterListWithComplexPredicate_thenSuccess(){
    List<String> result = names.stream()
      .filter(name -> name.startsWith("A") && name.length() < 5)
      .collect(Collectors.toList());
 
    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

这个选项比第一个跟灵活，因为我们可以使用按位运算来构建我们想要的复杂谓词。

## **5. Combining Predicates**

接下来，如果我们不想构造一个复杂的谓词通过按位运算，Java 8谓词 又有用的方法让我们可以使用来组合谓词。

我们将要使用方法 **Predicate.and()** ,**Predicate.or(), and Predicate.negate()**来组合谓词。

### **5.1. Predicate.and()**

在这个例子里，我们显示地定义了我们的谓词，然后我们使用*Predicate.and()*来组合他们：

``` java
@Test
public void whenFilterListWithCombinedPredicatesUsingAnd_thenSuccess(){
    Predicate<String> predicate1 =  str -> str.startsWith("A");
    Predicate<String> predicate2 =  str -> str.length() < 5;
   
    List<String> result = names.stream()
      .filter(predicate1.and(predicate2))
      .collect(Collectors.toList());
         
    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

如我们所见，这个语法相当直观，方法名字表明了操作的类型。使用 **and() **,我们已经过滤了我们的链表通过只提取了满足两个条件的名字。

### **5.2. Predicate.or()**

我们也可可以使用*Predicate.or()* 来组合谓词。

让我们来提取“J”开头的名字，以及名字长度小于四的名字：

``` java
@Test
public void whenFilterListWithCombinedPredicatesUsingOr_thenSuccess(){
    Predicate<String> predicate1 =  str -> str.startsWith("J");
    Predicate<String> predicate2 =  str -> str.length() < 4;
     
    List<String> result = names.stream()
      .filter(predicate1.or(predicate2))
      .collect(Collectors.toList());
     
    assertEquals(2, result.size());
    assertThat(result, contains("John","Tom"));
}
```

### **5.3. Predicate.negate()**

当我们组合我们的谓词的时候我们也可以使用*Predicate.negate()* ：

``` java
@Test
public void whenFilterListWithCombinedPredicatesUsingOrAndNegate_thenSuccess(){
    Predicate<String> predicate1 =  str -> str.startsWith("J");
    Predicate<String> predicate2 =  str -> str.length() < 4;
     
    List<String> result = names.stream()
      .filter(predicate1.or(predicate2.negate()))
      .collect(Collectors.toList());
     
    assertEquals(3, result.size());
    assertThat(result, contains("Adam","Alexander","John"));
}
```

这里，我们已经使用了一个**or()**和**negate()**以“J”开头或者长度小于4的names的条件来过滤链表。

### **5.4. Combine Predicates Inline** 组合谓词内联

我们不需要显示地使用*and(),*  *or()*, and *negate()*定义谓词。

我们也通过强制转换谓词来内敛它们：

``` java
@Test
public void whenFilterListWithCombinedPredicatesInline_thenSuccess(){
    List<String> result = names.stream()
      .filter(((Predicate<String>)name -> name.startsWith("A"))
      .and(name -> name.length()<5))
      .collect(Collectors.toList());
 
    assertEquals(1, result.size());
    assertThat(result, contains("Adam"));
}
```

## **6. Combining a Collection of Predicates**

最后，让我们看看怎么通过减少它们来链接一个谓词的集合（一组谓词）。

在下列的例子里，我们有一个我们使用了*Predicate.and()*组合的谓词的链表：

``` java
@Test
public void whenFilterListWithCollectionOfPredicatesUsingAnd_thenSuccess(){
    List<Predicate<String>> allPredicates = new ArrayList<Predicate<String>>();
    allPredicates.add(str -> str.startsWith("A"));
    allPredicates.add(str -> str.contains("d"));        
    allPredicates.add(str -> str.length() > 4);
     
    List<String> result = names.stream()
      .filter(allPredicates.stream().reduce(x->true, Predicate::and))
      .collect(Collectors.toList());
     
    assertEquals(1, result.size());
    assertThat(result, contains("Alexander"));
}
```

请注意我们使用了我们的基本特性如：

``` java
x->true
```

但是如果我们想要使用*Predicate.or()*来组合他们 会有所不同：

``` java
@Test
public void whenFilterListWithCollectionOfPredicatesUsingOr_thenSuccess(){
    List<String> result = names.stream()
      .filter(allPredicates.stream().reduce(x->false, Predicate::or))
      .collect(Collectors.toList());
     
    assertEquals(2, result.size());
    assertThat(result, contains("Adam","Alexander"));
}
```

## **7. Conclusion**

在这个文章里，我们探索了不同的方法来组合Java8中的谓词，通过使用**filter()**,建立复杂的谓词和组合的谓词。

完整的代码在[github地址](https://github.com/eugenp/tutorials/tree/master/core-java-8)



***



原文地址：[地址](https://www.baeldung.com/java-predicate-chain)

