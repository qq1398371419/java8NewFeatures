### java8 新特性学习
#### 【总览】
##### 一些重要的Java 8功能是:
- forEach() method in Iterable interface
- default and static methods in Interfaces
- Functional Interfaces and Lambda Expressions
- Java Stream API for Bulk Data Operations on Collections
- Java Time API
- Collection API improvements
- Concurrency API improvements
- Java IO improvements
- Miscellaneous Core API improvements

#### 【详情】
##### 1.forEach() method in Iterable interface(Iterable接口中的forEach（）方法)
> 每当我们需要遍历Collection时，我们需要创建一个Iterator，其目的是迭代，然后我们在循环中为Collection中的每个元素提供业务逻辑。如果没有正确使用迭代器，会抛出异常ConcurrentModificationException。

> Java 8在java.lang.Iterable接口中引入了forEach方法，这样在编写代码时我们只关注业务逻辑。 forEach方法将java.util.function.Consumer对象作为参数，因此它有助于将我们的业务逻辑放在我们可以重用的单独位置。让我们通过简单的例子看看每个用法。

##### 2.default and static methods in Interfaces(接口中的默认和静态方法)
> jdk8之前，interface方法不能有实现，但是从Java 8开始，接口被增强为具有实现方法。我们可以使用default和static关键字来创建具有方法实现的接口。例如Iterable接口中的forEach方法实现是

    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
	}
> 我们知道Java不会在类中提供多重继承，因为它会导致钻石问题。那么现在如何处理接口，因为接口现在类似于抽象类。解决方案是编译器将在此场景中抛出异常，我们将不得不在实现接口的类中提供实现逻辑。
##### 3.Functional Interfaces and Lambda Expressions（function接口和Lambda表达式）
> 如果你注意到上面的接口代码，你会注意到@FunctionalInterface注释。功能接口是Java 8中引入的新概念。**只有一个抽象方法的接口就变成了功能接口**。我们不需要使用@FunctionalInterface注释将接口标记为功能接口。 @FunctionalInterface注释是一种避免在功能界面中意外添加抽象方法的工具。您可以将其视为@Override注释，并且最佳实践是使用它。实例：java8 的runnable run接口，带有一个抽象方法:

    @FunctionalInterface
	public interface Runnable {
	    public abstract void run();
	}
> 功能接口的主要好处之一是可以使用lambda表达式来实例化它们。我们可以使用匿名类实例化一个接口，但代码看起来很笨重。

	//使用匿名类实例化
	Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("My Runnable");
    }
    };
> 由于功能接口只有一个方法，因此lambda表达式可以很容易地提供方法实现。我们只需要提供方法参数和业务逻辑。例如，我们可以使用lambda表达式将上面的实现编写为：


    //使用lambda表达式
    Runnable runnable1 = () -> System.out.println("My Runnable");
    runnable.run();
    runnable1.run();
> 如果在方法实现中有单个语句，我们也不需要花括号。例如，上面的Interface1匿名类可以使用lambda实例化，如下所示：

    Interface1 interface1 = (s) -> System.out.println(s);
    interface1.method1("interface1 method");

【lambda表达式扩展】
>  Java 中的 Lambda 表达式通常使用 (argument) -> (body) 语法书写，例如：
	
	(arg1, arg2...) -> { body }
	(type1 arg1, type2 arg2...) -> { body }
> Lambda 表达式的结构

- 一个 Lambda 表达式可以有零个或多个参数
- 参数的类型既可以明确声明，也可以根据上下文来推断。例如：(int a)与(a)效果相同
- 所有参数需包含在圆括号内，参数之间用逗号相隔。例如：(a, b) 或 (int a, int b) 或 (String a, int b, float c)
- 空圆括号代表参数集为空。例如：() -> 42
- 当只有一个参数，且其类型可推导时，圆括号（）可省略。例如：a -> return a*a
- Lambda 表达式的主体可包含零条或多条语句
- 如果 Lambda 表达式的主体只有一条语句，花括号{}可省略。匿名函数的返回类型与该主体表达式一致
- 如果 Lambda 表达式的主体包含一条以上语句，则表达式必须包含在花括号{}中（形成代码块）。匿- - 名函数的返回类型与代码块的返回类型一致，若没有返回则为空

【函数式接口扩展】
> 函数式接口是只包含一个抽象方法声明的接口,可以使用@FunctionalInterface标记
	
###### JDK 8之前已有的函数式接口
- java.lang.Runnable
- java.util.concurrent.Callable
- java.security.PrivilegedAction
- java.util.Comparator
- java.io.FileFilter
- java.nio.file.PathMatcher
- java.lang.reflect.InvocationHandler
- java.beans.PropertyChangeListener
- java.awt.event.ActionListener
- javax.swing.event.ChangeListener

###### 新定义的函数式接口
####### java.util.function中定义了几组类型的函数式接口以及针对基本数据类型的子接口。
- Predicate -传入一个参数，返回一个bool结果， 方法为boolean test(T t)
- Consumer -传入一个参数，无返回值，纯消费。 方法为void accept(T t)
- Function -传入一个参数，返回一个结果，方法为R apply(T t)
- Supplier -无参数传入，返回一个结果，方法为T get()
- UnaryOperator -一元操作符， 继承Function,传入参数的类型和返回类型相同。
- BinaryOperator -二元操作符， 传入的两个参数的类型和返回类型相同， 继承BiFunction。

【示例】

	Predicate<Integer> predicate = (i) -> i > 0;
	Consumer<Integer> consumer = (i) -> System.out.println("consumer : " + i);
	Function<Integer,Boolean> function = (i) -> i > 0;
	Supplier<Integer> supplier = () -> 1;
	UnaryOperator<Integer> unaryOperator = (i) -> i * i;
	BinaryOperator<Integer> binaryOperator = (i1,i2) -> i1 * i2;
	
	System.out.println(predicate.test(10));
	consumer.accept(10);
	System.out.println(function.apply(10));
	System.out.println(supplier.get());
	System.out.println(unaryOperator.apply(100));
	System.out.println(binaryOperator.apply(100,200));

##### 4.Java Stream API for Bulk Data Operations on Collections(用于集合上的批量数据操作的Java Stream API)
> 参考[Java 8 中的 Streams API 详解](https://www.ibm.com/developerworks/cn/java/j-lo-java8streamapi/index.html "Java 8 中的 Streams API 详解")

> 以下是示例代码


	


