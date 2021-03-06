# Java语言特性

共计12题。

### Q1: Java 语言的优点？

①平台无关性，摆脱硬件束缚，"一次编写，到处运行”。

②相对安全的内存管理和访问机制，避免大部分内存泄漏和指针越界。

③热点代码检测和运行时编译及优化，使程序随运行时间增长获得更高性能。

④完善的应用程序接口，支持第三方类库。



### Q2: Java 如何实现平台无关？

JVM：Java 编译器可生成与计算机体系结构无关的字节码指令，字节码文件不仅可以轻易地在任何机器上解释执行，还可以动态地转换成本地机器代码，转换是由JVM实现的，JVM是平台相关的，屏蔽了不同操作系统的差异。

语言规范：基本数据类型大小有明确规定，例如int永远为32位，而C/C++中可能是16位、32位，也可能是编译器开发商指定的其他大小。Java 中数值类型有固定字节数，二进制数据以固定格式存储和传输，字符串采用标准的Unicode格式存储。



### Q3: JDK和JRE的区别？

JDK：Java Development Kit，Java开发工具。提供了编译运行Java程序的各种工具，包括编译器、JRE及常用类库，是JAVA核心。.

JRE：Java Runtime Environment，Java运行环境。运行Java程序的必要环境，包括JVM、核心类库、核心配置工具。



### Q4：Java 按值调用还是引用调用？

按值调用指方法接收调用者提供的值，按引用调用指方法接收调用者提供的变量地址。

Java总是按值调用，方法得到的是所有参数值的副本，传递对象时实际上方法接收的是对象引用的副本。方法不能修改基本数据类型的参数，如果传递了一个int值，改变值不会影响实参，因为改变的是值的一个副本。

可以改变对象参数的状态，但不能让对象参数引用一个新的对象。如果传递了一个int数组，改变数组的内容会影响实参，而改变这个参数的引用并不会让实参引用新的数组对象。

### Q5：浅拷贝和深拷贝的区别？

浅拷贝：只复制当前对象的基本数据类型及引用变量，没有复制引用变量指向的实际对象。修改克隆对象可能影响原对象，不安全。

深拷贝：完全拷贝基本数据类型和引用数据类型，安全。





### Q6：什么是反射？

在运行状态中，对于任意一个类都能知道它的所有属性和方法，对于任意一个对象都能调用它的任意方法和属性，这种动态获取信息及调用对象方法的功能称为反射。缺点是破坏了封装性以及泛型约束。反射是框架的核心，Spring 大量使用反射。

### Q7：Class 类的作用？如何获取一个Class对象？

在程序运行期间，Java 运行时系统为所有对象维护一个运行时类型标识，这个信息会跟踪每个对象所属的类，虚拟机利用运行时类型信息选择要执行的正确方法，保存这些信息的类就是Class、这是一个泛型类。
获取Class对象：①类名.class。②对象的getClass方法。③Class.forName（类的全限定名）。

### Q8：什么是注解？什么是元注解？

注解是一种标记，使类或接口附加额外信息，帮助编译器和JVM完成一些特定功能，例如@Override标识一个方法是重写方法。

元注解是自定义注解的注解，例如：

@Target：约束作用位置，值是ElementType枚举常量，包括METHOD方法、VARIABLE 变量、TYPE类/接口、PARAMETER方法参数、CONSTRUCTORS 构造方法和LOACL_ _VARIABLE局部变量等。

@Rentention：约束生命周期，值是RetentionPolicy枚举常量，包括SOURCE源码、CLASS 字节码和RUNTIME运行时。

@Documented：表明这个注解应该被javadoc记录。



### Q9：什么是泛型，有什么作用？

泛型本质是参数化类型，解决不确定对象具体类型的问题。泛型在定义处只具备执行Object方法的能力。

泛型的好处：

①类型安全，放置什么出来就是什么，不存在ClassCastException。

②提升可读性，编码阶段就显式知道泛型集合、泛型方法等处理的对象类型。

③代码重用，合并了同类型的处理代码。



### Q10：泛型擦除是什么：

泛型用于编译阶段，编译后的字节码文件不包含泛型类型信息，因为虚拟机没有泛型类型对象，所有对象都属于普通类。例如定义List<object>或List<string> ，在编译后都会变成List。

定义一个泛型类型，会自动提供一个对应原始类型，类型变量会被擦除。如果没有限定类型就会替换为Object，如果有限定类型就会替换为第一个限定类型，例如<T extends A & B>会使用A类型替换T。

### Q11：JDK8新特性有哪些？

lambda表达式：允许把函数作为参数传递到方法，简化匿名内部类代码。

函数式接口：使用@FunctionalInterface标识，有且仅有一个抽象方法，可被隐式转换为lambda表达式。

方法引用：可以引用已有类或对象的方法和构造方法，进一步简化lambda表达式。

接口：接口可以定义default修饰的默认方法，降低了接口升级的复杂性，还可以定义静态方法。

注解：引入重复注解机制，相同注解在同地方可以声明多次。注解作用范围也进行了扩展，可作用于局部变量、泛型、方法异常等。

类型推测：加强了类型推测机制，使代码更加简洁。

Optional类：处理空指针异常，提高代码可读性。

Stream类：引入函数式编程风格《提供了很多功能，使代码更加简洁。方法包括forEach遍历、count统计个数、filter 按条件过滤、limit 取前n个元素、skip 跳过前n个元素、map映射加工、concat 合并stream流等。

日期：增强了日期和时间API，新的java.time包主要包含了处理日期、时间、日期/时间、时区、时刻和时钟等操作。

JavaScript：提供了一个新的JavaScript引擎，允许在JVM上运行特定JavaScript应用。

### Q12：异常有哪些分类？

所有异常都是Throwable的子类，分为Error和Exception。Error 是Java运行时系统的内部错误和资源耗尽错误，例如StackOverFlowError和OutOfMemoryError,这种异常程序无法处理。

Exception分为受检异常和非受检异常，受检异常需要在代码中显式处理，否则会编译出错，非受检异常是运行时异常，继承自RuntimeException。

受检异常：

①无能为力型，如字段超长导致的SQLException。

②力所能及型，如未授权异常UnAuthorizedException,程序可跳转权限申请页面。常见受检异常还有FileNotFoundException、ClassNotFoundException、 l0Exception等 。

非受检异常：

①可预测异常，例如IndexOutOfBoundsException、NullPointerException、 ClassCastException
等，这类异常应该提前处理。

②需捕捉异常，例如进行RPC调用时的远程服务超时，这类异常客户端必须显式处理。

③可透出异常，指框架或系统产生的且会自行处理的异常，例如Spring的NoSuchRequestHandingMethodException，Spring 会自动完成异常处理，将异常自动映射到合适的
状态码。