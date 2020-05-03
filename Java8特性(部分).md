# Java8特性

a.随着大数据的兴起，函数式编程在处理大数据上的优势开始体现，引入了Lambada函数式编程

b.使用Stream彻底改变了集合使用方式：只关注结果，不关心过程

c.新的客户端图形化工具界面库：JavaFX

d.良好设计的日期/时间API

e.增强的并发/并行API

f.Java与JS交互引擎 -nashorn

g.其他特性

## Lambda表达式

带有参数变量的表达式，是一段可以传递的代码，可以被一次或多次执行

是一种精简的字面写法，其实就是把匿名内部类中“一定”要做的工作省略掉

然后由JVM通过推导把简化的表达式还原

格式： (parameters参数) -> expression表达式或方法体

parameters:类似方法中的形参列表，这里的参数是函数式接口里的参数

->：可理解为“被用于”的意思

方法体：可以是表达式也可以代码块，是函数式接口里方法的实现

如果负责运算的代码无法用表达式表示，可以使用编写方法实现

但必须用{}包围并按需明确使用 return语句

> ```java
> public class Demo01 {
> 	public static void main(String[] args) {
> 		// 定义字符串数组
> 		String[] strArr = { "abc", "cd", "abce", "a" };
>  
> 		// 传统方法
> 		// 匿名内部类
> 		Arrays.sort(strArr, new Comparator<String>() {
>  
> 			@Override
> 			public int compare(String s1, String s2) {
> 				return Integer.compare(s2.length(), s1.length());
> 			}
> 		});
>  
> 		// 输出排序结果
> 		for (String s : strArr) {
> 			System.out.println(s);
> 		}
> 		System.out.println("---------------------");
>  
> 		// Lambda表达式
> 		Arrays.sort(strArr, (s1, s2) -> Integer.compare(s2.length(), s1.length()));
>  
> 		// 输出
> 		for (String s : strArr) {
> 			System.out.println(s);
> 		}
> 
> ```

用Lambda实现多线程

```java

 
public class Demo01 {
	public static void main(String[] args) {
		// Lambda表达式
		new Thread(() -> System.out.println(1 + "hello world")).start();
 
		System.out.println("----------------");
 
		// 方法体
		new Thread(() -> {
			for (int i = 0; i < 10; i++) {
				System.out.println(2 + "hello world");
			}
		}).start();
	}
}

```

需要显示创建函数式接口对象的地方，都可以使用lambda



## 函数式接口分类

### 系统与定义函数接口



### 用户自定义函数接口

```java
//(注解必须有，表达式是直接通过参数列表来实现的，只能有一个有效方法)
@FunctionalInterface
public interface MyInterface{
    String info(String tip);
}
```



### 公共定义的函数式接口

	>a.功能性接口：Function<T, R>
	>
	>​	有输入参数，有返回值
	>
	>​	是对接收一个T类型参数，返回R类型的结果的方法的抽象
	>
	>​	通过调用apply方法执行内容
	>
	>​	需求：给定一个字符串，返回字符串长度

```java

public class Demo01 {
	public static void main(String[] args) {
		// 定义字符串
		String str = "helloworld";
		
		// 调用方法
		// 在调用的时候写方法体，方法比较灵活
		int length = testFun(str, (s) -> s.length());
		
		System.out.println(length);
	}
	
	// 方法
	/**
	 * 
	 * @param str 输入参数
	 * @param fun 表达式 String 为输入类型，Integer为输出类型
	 * @return 返回字符串长度
	 */
	public static int testFun(String str, Function<String, Integer> fun) {
		// 执行
		Integer length = fun.apply(str);
		
		return length;
	}
}

```

> b.消费型接口：Consumer<T>
>
> 有输入参数，没返回值
>
> 对应的方法类型为接收一个参数，没有返回值
>
> 一般来说使用Consumer接口往往伴随着一些期望状态的改变
>
> 或者事件的发生，典型的forEach就是使用的Consumer接口
>
> 虽然没有任何的返回值，但是向控制台输出结果
>
> Consumer 使用accept对参数执行行为
>
> 需求：输出字符串

```java
public class Demo1{
    public static void main(String[] args){
        //创建字符串
        String str="hello,world";
        //调用
        testCon(str,(s) ->System.out.println(s));
    }
    /**
	 * 
	 * @param str 传入参数
	 * @param con
	 */
    public static void testCon(String str,Consumer<String> con){
        //执行
       	con.accept(str);
    }
}
```



>c.供给型接口：Supplier<T>
>
>无传入参数，有返回值
>
>该接口对应的方法类型不接受参数，但是提供一个返回值
>
>使用get()方法获得这个返回值

```java
public class Demo01 {
	public static void main(String[] args) {
		// 创建字符串
		String str = "hello world";
		
		// 调用
		String sup = testSup(() -> str);
		
		System.out.println(sup);
	}
	
	/**
	 * 
	 * @param sup
	 * @return
	 */
	public static String testSup(Supplier<String> sup) {
		// 执行
		String s = sup.get();
		return s;
	}
	
}
```



> d.断言型接口：Predicate<T>
>
> 有传入参数，有返回值Boolean
>
> 该接口对应的方法为接收一个参数，返回一个Boolean类型值
>
> 多用于判断与过滤，使用test()方法执行这段行为
>
> 需求：输入字符串，判断长度是否大于0

```java

public class Demo01 {
	public static void main(String[] args) {
		// 创建字符串
		String str = "hello world";
		
		// 调用
		boolean flag = testPre(str, (s) -> s.length() > 0);
		
		System.out.println(flag);
	}
	
	/**
	 * 
	 * @param str
	 * @param pre
	 * @return
	 */
	public static boolean testPre(String str, Predicate<String> pre) {
		// 执行
		boolean flag = pre.test(str);
		
		return flag;
	}
	
}
```



>6.Lambda的优点
>
>​	a.极大的减少代码冗余，同时可读性也好过冗长的匿名内部类
>
>​	b.与集合类批处理操作结合，实现内部迭代，并充分利用现代多核CPU进行并行计算。之前集合类的迭代都是外部的，即客户代码。而内部迭代意味着由Java类库来进行迭代，而不是客户代码
>
>7.和匿名内部类的区别
>
>​	a.在lambda中，this不是指向lambda表达式产生的那个对象，而是它的外部对象
>
>​	b.Java 编译器编译 Lambda 表达式并将他们转化为类里面的私有函数，它使用 Java 7 中新加的 invokedynamic 指令动态绑定该方法，但每一个匿名内部类编译器会为其创建一个类文件



## Stream

Stream 中文称为 “流”，通过将集合转换为这么一种叫做 “流” 的元素序列，通过声明性方式，能够对集合中的每个元素进行一系列并行或串行的流水线操作。

函数式编程带来的好处尤为明显。这种代码更多地表达了业务逻辑的意图，而不是它的实现机制。易读的代码也易于维护、更可靠、更不容易出错。



>Stream概述
>
>是用函数式编程方式在集合类上进行复杂操作的工具，更像一个高级版本的 Iterator
>
>原始版本的 Iterator，用户只能显式地一个一个遍历元素并对其执行某些操作
>
>高级版本的 Stream，用户只要给出需要对其包含的元素执行什么操作
>
>Stream 会隐式地在内部进行遍历，做出相应的数据转换
>
>而和迭代器又不同的是，Stream 可以并行化操作
>
>借助于 Lambda 表达式，极大的提高编程效率和程序可读性



> 常用操作
>
> >1.forEach
> >
> >迭代集合中元素。接收一个 Lambda 表达式
> >
> >然后在 Stream 的每一个元素上执行该表达式
> >
> >此操作是及早求值方法
> >
> >​	
> >
> >2.collect(tolist())
> >
> >由Stream 里的值生成一个列表，是一个及早求值操作
> >
> >很多Stream 操作都是惰性求值，因此调用Stream 一系列方法之后
> >
> >还需最后再调用一个类似collect 的及早求值方法返回集合
> >
> >开篇的例子：(再将符合要求的字符串放到一个新的集合里)
> >
> >使用filter过滤器：遍历数据并检查其中的元素
> >

```java
	public static void main(String[] args) {
		// 创建集合
		List<String> list = new ArrayList<>();
 
		// 添加元素
		list.add("sdf");
		list.add("a");
		list.add("asdf");
		list.add("d");
		list.add("basdfgh");
 
		// 统计长度大于2的
		long count = list.stream().filter((s) -> s.length() > 2).count();
 
		// 将符合要求的放入集合
		List<String> list2 = list.stream().filter((s) -> s.length() > 2).collect(Collectors.toList());
 
		System.out.println(count);
		list2.forEach(System.out :: println);
	}
 
}
```



> map
>
> 如果有一个函数可以将一种类型的值转换成另外一种类型
>
> map 操作就可以使用该函数，将一个流中的值转换成一个新的流
>
> 需求：将字符串全转换成大写

```java
public static void main(String[] args) {
		// 创建集合
		List<String> list = new ArrayList<>();
 
		// 添加元素
		list.add("sdf");
		list.add("a");
		list.add("asdf");
		list.add("d");
		list.add("basdfgh");
		
		// 转换为大写
		List<String> list2 = list.stream().map((s) -> s.toUpperCase()).collect(Collectors.toList());
		
		list2.forEach(System.out :: println);
 
	}
```



> filter
>
> 遍历数据并检查其中的元素。例如获取字符串List中以数字开始的字符集合

```java
		// 创建集合
		List<String> list = new ArrayList<>();
 
		// 添加元素
		list.add("1sdf");
		list.add("a");
		list.add("2asdf");
		list.add("d");
		list.add("basdfgh");
 
		// 获取数字开头的
		List<String> list2 = list.stream().filter((s) -> Character.isDigit(s.charAt(0))).collect(Collectors.toList());
 
		list2.forEach(System.out::println);
	}
```



> flatMap 
>
> 可用Stream 替换值， 然后将多个Stream 连接成一个Stream
>
> map 操作可用一个新的值代替Stream 中的值
>
> 若用户希望让map操作有点变化
>
> 生成一个新的Stream 对象取而代之则flatMap适用
>
> 假设有一个包含多个列表的流，现在希望得到所有数字的序列

```java
 
public static void main(String[] args) {
	//生成，连接两个list的stream流。	用flatmap生成一个新的steam流
    Stream<?> flatMap = Stream.of(Arrays.asList("a", "b"), Arrays.asList(1, 2,3)).flatMap((s) -> s.stream());
    
		flatMap.forEach(System.out :: println);
	}

```



> max和min
>
> 获取Stream中最大值或最小值，获取字符串List中长度最长的字符串长度

```java
public static void main(String[] args) {
		List<String> list = new ArrayList<>();
		
		list.add("abc");
		list.add("ab");
		list.add("abcd");
		list.add("abcde");
		
		// 获取最大值
		int max = list.stream().map((s) -> s.length()).max(Integer :: compareTo).get();
		System.out.println(max);
		
		// 获取最小值，另一种方法
    	//Comparator适用于比较容器
		int min = list.stream().min(Comparator.comparing((s) -> s.length())).get().length();
		System.out.println(min);
	}
```



> reduce
>
> 通过指定的函数把stream中的多个元素汇聚为一个元素，min max等是它的特例
>
> 格式：reduce(初始值，(r, i) -> r + i) 或者 reduce((r, i) -> r + "*" i)
>
> 计算1~100的和

```java
public static void main(String[] args) {
		List<Long> list = new ArrayList<>();
		
		// 封装到集合
		for (long i = 1; i <= 100; i++) {
			list.add(i);
		}
		
		// 计算
		// reduce：参1，和的初始值
    	//r:和，l:数
		Long sum = list.stream().parallel().reduce(0L, (r, l) -> r + l);
		
		System.out.println(sum);
	}

```



练习：

```java
public static void main(String[] args) {
		List<Student> list = new ArrayList<>();
 
		Student s1 = new Student("张三", 21);
		Student s2 = new Student("李四", 19);
		Student s3 = new Student("王五", 18);
		Student s4 = new Student("程六", 17);
		Student s5 = new Student("赵七", 20);
 
		list.add(s1);
		list.add(s2);
		list.add(s3);
		list.add(s4);
		list.add(s5);
 
		// 筛选
		Student student = list.stream().filter((s) -> s.getAge() < 20).max(Comparator.comparing((s) -> s.getAge()))
				.get();
		System.out.println(student);
	}
```

查找集合中以a开头的字符的长度集合

```java
public static void main(String[] args) {
		List<Integer> list = Stream.of("abc", "b", "a", "abcd").filter((s) -> s.startsWith("a")).map((s) -> s.length()).collect(Collectors.toList());
    //map:如果有一个函数可以将一种类型的值转换成另外一种类型，map 操作就可以使用该函数，将一个流中的值转换成一个新的流

		System.out.println(list);
	}
```



## 并行

在Java8中Stream接口应用分支/合并框架

将一个数据内容分成多个数据块，并用不同的线程分别处理每个数据块流

Stream有串行和并行两种，串行Stream上的操作是在一个线程中依次完成

而并行Stream则是在多个线程上同时执行



### 使用方法

>a.调用Stream的parallel()方法
>
>b.调用Collection的parallelStream()方法
>
>c.parallel() 与 sequential() 可在并行流与顺序流之间切换



### 使用并行的建议

>a.尽量使用基本类型的流  IntStream, LongStream, and DoubleStream
>
>b.有些操作使用并发流的性能会比顺序流的性能更差，比如limit，findFirst，依赖元素顺序的操作在并发流中是极其消耗性能的.findAny的性能就会好很多，因为不依赖顺序
>
>c.考虑流中计算的性能(Q)和操作的性能(N)的对比， Q表示单个处理所需的时间， N表示需要处理的数量，如果Q的值越大, 使用并发流的性能就会越高
>
>d.数据量不大时使用并发流，性能得不到提升
>
>e.考虑数据结构：并发流需要对数据进行分解，不同的数据结构被分解的性能时不一样的
>
>f.传递给并行流的函数都是线程安全的，无副作用



### 何时需要并行编程

>
>
>a.是否需要并行
>
>>弄清楚你要解决的问题是什么，数据量有多大，计算的特点是什么
>>
>>并不是所有的问题都适合使用并发，比如当数据量不大时
>>
>>顺序执行往往比并行执行更快。
>>
>>准备线程池和其它相关资源也是需要时间的
>>
>>但当任务涉及到I/O操作并且任务之间不互相依赖时，那么适合并行化
>
>b.任务之间是否是独立的？是否会引起任何竞态条件
>
>> 如果任务之间是独立的
>>
>> 并且代码中不涉及到对同一个对象的某个状态或者某个变量的更新操作
>>
>> 那么就表明代码是可以被并行化的
>
>c.结果是否取决于任务的调用顺序
>
>> 由于在并行环境中任务的执行顺序是不确定的
>>
>> 因此对于依赖于顺序的任务而言，并行化也许不能给出正确的结果





LocalDate

>表示不带时区的日期，比如2014-01-14
>
>a.LocalDateTime.now();获取当前日期时间
>
>b.now.minusDays(2);将当前日期时间减去两天
>
>c.LocalDateTime.of(2016, 10, 23);构造一个指定日期时间的对象

```java
public static void main(String[] args) {
		// 当前时间
		LocalDate now = LocalDate.now();
		System.out.println(now);
		
		// 往前推两天
		LocalDate date = now.minusDays(2);
		System.out.println(date);
		
		// 制定一个日期
		LocalDate localDate = LocalDate.of(1993, 2, 6);
		System.out.println(localDate);
	}
```



LocalTime

>表示不带时区的时间
>
>a. LocalTime.now();当前时间
>
>b. LocalTime.of(22, 33);确定的时间
>
>c.LocalTime.ofSecondOfDay(4503); 一天中的第4503秒

```java
public static void main(String[] args) {
		// 当前时间
		LocalTime now = LocalTime.now();
		System.out.println(now);
		
		// 22:33
		LocalTime localTime = LocalTime.of(22, 33);
		System.out.println(localTime);
		
		// 一天中的4503秒
		LocalTime ofDay = LocalTime.ofSecondOfDay(4503);
		System.out.println(ofDay);
	}

```



LocalDateTim

>是LocalDate和LocalTime的组合体，表示的是不带时区的日期及时间
>
>a.LocalDateTime.now();当前时间
>
>b.localDateTime.plusHours(25).plusMinutes(3); 当前时间加上25小时３分钟
>
>c.LocalDateTime.of(2014, 1, 20, 3, 30, 20)；转换

```java
public static void main(String[] args) {
		// 当前时间
		LocalDateTime now = LocalDateTime.now();
		System.out.println(now);
 
		// 当前时间加上25小时３分钟
		LocalDateTime plusMinutes = now.plusHours(25).plusMinutes(3);
		System.out.println(plusMinutes);
 
		// 转换
		LocalDateTime of = LocalDateTime.of(1993, 2, 6, 11, 23, 30);
		System.out.println(of);
	}
```



ZoneDateTime

>含有时区信息的时间
>
>a.ZonedDateTime.now();获取包含时区的当前日期时间
>
>b.ZonedDateTime.of(LocalDateTime.of(2014, 1, 20, 3, 30, 20), ZoneId.of("+08"));创建时区的日期时间对象

```java
public static void main(String[] args) {
		// 当前时间
		ZonedDateTime now = ZonedDateTime.now();
		System.out.println(now);
 
		// 创建时区的日期时间对象
		ZonedDateTime of = ZonedDateTime.of(LocalDateTime.of(1993, 2, 6, 11, 23, 30), ZoneId.of("+08"));
		System.out.println(of);
	}
```



DateTimeFormatter

>格式化日期和解析日期格式字符串。DateTimeFormatter是不可变类
>
>a.格式化：日期对象转换为格式字符串

````java
public static void main(String[] args) {
		// 格式化
		String time = DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss").format(LocalDateTime.now());
		System.out.println(time);
	}
````



格式字符串转换为日期对象

```java
public static void main(String[] args) {
		// 格式化
		LocalDateTime parse = LocalDateTime.parse("2017.01.01 08:08:08", DateTimeFormatter.ofPattern("yyyy.MM.dd HH:mm:ss"));
		System.out.println(parse);
	}
```



## **其他**

### 1.默认方法

> 接口和它的实现类保持兼容非常重要
>
> 因为你开发的类库可能正在被多个开发者广泛的使用着
>
> 若上层接口需要做改变的时候（如增加新的空方法）
>
> 下层接口就需要实现新增的方法，在某些情况下，变得不灵活
>
> 因此：使用默认方法
>
> 
>
> 接口中定义的default修饰的有具体实现的方法，是给接口增加的公用方法
>
> 是对象方法，需要使用对象来进行访问
>
> 添加默认方法，对以前代码不会有影响，避免其实现类都做出相应的改动
>
> 且default方法通常使用已经实现类已存在的方法进行定义



### 2.

> 如果一个接口中定义一个默认方法
>
> 另外一个父类或接口中定义同名方法(不管是否default)时候，子类如何处理
>
> a.选择父类方法。当父类提供具体实现时，接口同名同参方法被忽略
>
> b.覆盖父类方法。当一个父接口提供默认方法，另一个父接口提供同名同参方法时候



### 3.静态方法

> 接口中定义的static修饰的有具体实现的方法
>
> 类似于default方法，但在实现类中不能覆盖(override)该方法
>
> 接口的static方法只对接口里的方法可见，和其他静态方法类似
>
> 可以使用接口名来调用接口中的静态方法
>
> 接口的static方法有利于提供通用的方法，比如判空检查，集合排序等
>
> 可以移除工具类中方法到接口static方法中
>
> 例如：sList.sort((s,t)->Integer.compare(s.length(), t.length()));
>
> 转换为静态方法：sList.sort(Comparator.comparing(String::length));



### 4.String新增join(字符串集合或数组)方法

>使用指定连接符连接多个字符串

```java
public static void main(String[] args) {
		String[] str = {"a", "b", "c"};
		
		String join = String.join("-", str);
		System.out.println(join);
	}
```



### 5.可能错过的Java7特性

> try--with-resources
>
> > 在 JDK 7 之前，文件资源需要手动finally中关闭
> >
> > try-with-resources 能够很容易地关闭在 try-catch 语句块中使用的资源
> >
> > 所谓的资源（resource）是指在程序完成后，必须关闭的对象
> >
> > try-with-resources 语句确保了每个资源在语句结束时关闭
> >
> > 所有实现了 java.lang.AutoCloseable 接口的对象可以使用作为资源
> >
> > 在try( ...)里声明的资源，会在try-catch代码块结束后自动关闭掉