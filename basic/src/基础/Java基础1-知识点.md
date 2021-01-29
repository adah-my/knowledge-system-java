著作权归https://www.pdai.tech所有。
链接：https://www.pdai.tech/md/java/basic/java-basic-lan-basic.html


###### Java 基础 - 知识点

**数据类型 包装类型 缓存池** 

    八个基本类型/(bit)：
        boolean/1   byte/8  char/16   short/16    int/32    float/32    long/64    double/64
    
        基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。
        Integer x = 2;  // 装箱
        int y = x;      // 拆箱
    
    缓存池
         new Integer(123) 与 Integer.valueOf(123) 的区别在于：
            new Integer(123) 每次都会新建一个对象
            Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一对象的引用。 （调用时先判断值是否在缓存池中，如果在就直接从缓存池中取）
         
         编译器会在自动装箱过程调用 valueOf() 方法，因此多个 Integer 实例使用自动装箱来创建并且值相同，那么就会引用相同的对象。
            Integer m = 123;
            Integer n = 123;
            System.out.println(m == n); // true
            
         基本类型对应的缓冲池如下：
            boolean value true and false
            add byte values
            short values between -128 and 127
            int values between -128 and 127
            char in the range \u0000 to \u007F
            在使用这些基本类型对应的包装类型时，就可以直接使用缓冲池中的对象。




**String 概览 不可变的好处 String, StringBuffer and StringBuilder String.intern()** 
    
    概览
        String 被声明为 final，因此它不可以被继承。
        内部使用 char 数组存储数据，该数组被声明为 final，这意味着 value 数组初始化之后就不能再引用其他数组。并且 String 内部没有改变 value 数组的方法，因此可以保证 String 不可变。
        public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
            /** The value is used for character storage. */
            private final char value[];

    不可变的好处
        1.可以缓存hash值：因为 String 的 hash 值经常被使用，例如 String 用作 HashMap 的 key。不可变的特性使得 hash 值也不可变，因此只需要进行一次计算。
        2.String Pool 的需要：如果一个 String 对象已经被创建过了，那么就会从 String Pool 中取得引用。只有 String 是不可变的才可能使用 String Pool。
        3.安全性：String 经常作为参数，String 不可变性可以保证参数不可变。
        4.线程安全：String 不可变性天生具备线程安全，可以在多个线程中安全地使用。
    
    String, StringBuffer and StringBuilder
        1.可变性
            String 不可变
            StringBuffer 和 StringBuilder 可变
        2.线程安全
            String 不可变，因此是线程安全的
            StringBuilder 不是线程安全的
            StringBuffer 是线程安全的，内部使用 synchronized 进行同步 
    
    String.intern()
        使用 String.intern() 可以保证相同内容字符串变量引用同一的内存对象。
    
        intern() 首先会把 str 引用的对象放到 String Pool(字符串常量池)中，然后返回这个对象引用。因此 s3 和 s1 引用的是同一字符串常量池的对象。
            String s1 = new String("aaa");
            String s2 = new String("aaa");
            System.out.println(s1 == s2);           // false
            String s3 = s1.intern();
            System.out.println(s1.intern() == s3);  // true
        
        如果采用 "bbb" 这种双引号的形式创建字符串实例，会自动地将新建地对象放入 String Pool 中。
            String s4 = "bbb";
            Stirng s5 = "bbb";
            System.out.println(s4 == s5);   // true

        在 Java 7 之前，字符串常量池被放在运行时常量池中，它属于永久代。而在 Java 7，字符串常量池被移到 Native Method 中。
        这是因为永久代地空间有限，在大量使用字符串地场景下会导致 OutOfMemoryError 错误。
    
  
    
**运算 参数传递 float 与 double 隐式类型转换 switch**    
    
    参数传递
        Java 的参数是以值传递的形式传入方法中，而不是引用传递。
        
        以下代码中 Dog dog 的 dog 是一个指针，存储的是对象的地址。在将一个参数传入一个方法时，本质上是将对象的地址以值的方式传递到形参中。
        因此在方法中改变指针引用的对象，那么这两个指针指向的是完全不同的对象，一方改变其所指向对象的内容对另一方没有影响。 
            public class PassByValueExample {
                public static void main(String[] args) {
                    Dog dog = new Dog("A");
                    System.out.println(dog.getObjectAddress()); // Dog@4554617c
                    func(dog);
                    System.out.println(dog.getObjectAddress()); // Dog@4554617c
                    System.out.println(dog.getName());          // A
                }
            
                private static void func(Dog dog) {
                    System.out.println(dog.getObjectAddress()); // Dog@4554617c
                    dog = new Dog("B");
                    System.out.println(dog.getObjectAddress()); // Dog@74a14482
                    System.out.println(dog.getName());          // B
                }
            }
        但是如果在方法中改变对象的字段值会改变原对象该字段值，因为改变的是同一个地址指向的内容。
            class PassByValueExample {
                public static void main(String[] args) {
                    Dog dog = new Dog("A");
                    func(dog);
                    System.out.println(dog.getName());          // B
                }
            
                private static void func(Dog dog) {
                    dog.setName("B");
                }
            }
    
    float 与 double
        1.1 字面量属于 double 类型，不能直接将 1.1 直接赋值给 float 变量，因为这是向下转型。Java 不能隐式执行向下转型，因为这会使得精度降低。
            // float f = 1.1  error
        1.1f 字面量才是 float 类型。
            float f = 1.1f;
    
    隐式类型转换
        因为字面量 1 是 int 类型，它比 short 类型精度要高，因此不能隐式地将 int 类型下转型为 short类型。
            short s1 = 1;
            // s1 = s1 + 1;  error
        但是使用 += 运算符可以执行隐式类型转换。
            s1 += 1;    
        上面地语句相当于将 s1 + 1 的计算结果进行了向下转型：
            s1 = (short)(s1 + 1);
    
    switch
        从 Java 7 开始，可以在 switch 条件判断语句中使用 String 对象。
            String s = "a";
            switch(s) {
                case "a" : System.out.println("aaa"); break;
                case "b" : System.out.println("bbb"); break;
            }
        switch 不支持 long，是因为 switch 的设计初衷是对那些只有少数几个值进行等值判断，如果值过于复杂，那么还是 if 比较合适。
            // long x = 111;
            // switch (x) { // Incompatible types. Found: 'long', required: 'char, byte, short, int, Character, Byte, Short, Integer, String, or an enum'
            //    case 111: System.out.println(111); break;
            //    case 222: System.out.println(222); break;
            // }             
            // error
    
    

**继承 访问权限 抽象类与接口 super 重写与重载**
    
    访问权限
        Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。
        可以对类或类中的成员（字段以及方法）加上访问修饰符。
            类可见表示其他类可以用这个类创建实例对象；
            成员可见表示其他类可以用这个类的实例对象访问到该成员；
            protected 用于修饰成员，表示在继承体系中成员对于子类可见，但是这个访问修饰符对于类没有意义。
    
        设计良好的模块会隐藏所有实现的细节，把它的 API 与它的实现清晰的隔离开来。模块之间只通过它们的 API 进行通信，一个模块不需要知道其他模块的内部工作情况，这个概念被称为封装。    
        因此访问权限应当尽可能地使每个类或者成员不被外界访问。
        
        如果子类的方法重写了父类的方法，那么子类中该方法地访问级别不允许低于父类地访问级别。这是为了确保可以使用父类实例的地方都可以使用子类实例，也就是为了确保里氏替换原则。
        
        字段决不能是公有的，因为这么做的话就失去了对这个字段修改行为的控制，客户端可以对其随意修改。
        
        但是也有例外，如果是包级私有的类或者私有的嵌套类，那么直接暴露成员不会有特别大地影响。
            public class AccessWithInnerClassExample {
                private class InnerClass{ int x; }
                
                private InnerClass innerClass;
                
                public AccessWithInnerClassExample(){
                    innerClass = new InnerClass();
                }
                
                public int getValue(){
                    return innerClass.x; // 直接访问
                }
            }

    抽象类与接口
        1.抽象类
            抽象类和抽象方法都使用 abstract 关键字进行声明。抽象类一般会包含抽象方法，抽象方法一定位于抽象类中。
            抽象类和普通类最大的区别是，抽象类不能被实例化，需要继承抽象类才能实例化其子类。
    
        2.接口
            接口是抽象类地延申，在 Java 8 之前，它可以看成是一个完全抽象的类，也就是说它不能有任何的方法实现。
            从 Java 8 开始，接口也可以拥有默认的方法实现，这是因为不支持默认方法的接口维护成本太高了。在 Java 8 之前，如果一个接口想要添加新的方法，那么要修改所有实现了该接口的类。
            接口的成员（字段 + 方法）默认都是 public 的，并且不允许定义为 private 或者 protected。
            接口的字段默认都是 static 和 final 的。

        3.比较
            从设计层面上看，抽象类提供了一种 IS-A关系，那么就必须满足里氏替换原则，即子类对象必然能够替换掉所有父类对象。
            而接口更像是一种 LIKE-A 关系，它只是提供一种方法实现契约，并不要求接口和实现接口的类具有 IS-A 关系。
            从使用上来看，一个类可以实现多个接口，但是不能继承多个抽象类。
            接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
            接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。
            
        4.使用选择
            使用接口：
                需要不相关的类都实现一个方法，例如不相关的类都可以实现 Compareable 接口中的 compareTo() 方法；
                需要使用多种继承；
            使用抽象类：
                需要在几个相关的类中共享代码。
                需要能控制继承来的成员的访问权限，而不是都为 public。
                需要继承非静态和非常量字段。
            在很多情况下，接口优先于抽象类，因为接口没有抽象类严格的类层次结构要求，可以灵活地为一个类添加行为。并且从 Java 8 开始，接口也可以有默认的方法实现，使得修改接口的成本也变得很低。

    super
        访问父类的构造函数：可以使用 super() 函数访问弗雷德构造函数，从而委托父类完成一些初始化工作。
        访问父类的成员：如果子类重写了父类中的某个方法的实现，可以通过使用 super 关键字来引用父类的方法实现。
        
    重写与重载
        1.重写(Override)
            存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。
            为了满足里氏替换原则，重写有以下两个限制：
                子类方法的访问权限必须大于等于父类方法；
                子类方法的返回类型必须是父类方法返回类型或者为其子类型。
            使用 @Override 注解，可以让编译器帮忙检查是否满足上面的两个限制条件。
            
        2.重载(Overload)
            存在一同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。
            应该注意的是，返回值不同，其他都相同不算是重载。
    
    

**Object 通用方法 概览 equals() hashCode() toString() clone()**   

    equals()
        1.等价关系
            1.自反性   x.equals(x)     // true
            2.对称性   x.equals(y) == y.equals(x); // true
            3.传递性   if (x.equals(y) && y.equals(z))  x.equals(z); // true;
            4.一致性   多次调用 equals() 方法结果不变
            5.与 null 的比较  对任何不是 null 的对象 x 调用 x.equals(null) 结果都为 false
    
        2.equals() 与 ==
            对于基本类型，== 判断两个值是否相等，基本类型没有 equals() 方法。
            对于引用类型，== 判断两个变量是否引用用一个对象，而 equals() 判断引用的对象是否等价。
                Integer x = new Integer(1);
                Integer y = new Integer(1);
                System.out.println(x.equals(y));    // true
                System.out.println(x == y);         // false;

        3.实现
            检查是否为同一个对象的引用，如果是直接返回 true
            检查是否为同一个类型，如果不是，直接返回 false
            将 Object 对象进行转型
            判断每个关键域是否相等
    
    hashCode()
        hashCode() 返回散列值，而 equals() 是用来判断两个对象是否等价。等价的两个对象散列值一定相同，但是散列值相同的两个对象不一定等价。
        
        在覆盖 equals() 方法时应当总是覆盖 hashCode() 方法，保证等价的两个对象散列值也相等。
        
        下面的代码中，新建了两个等价的对象，并将它们添加到 HashSet 中。
        我们希望这降两个对象当成一样的，只在集合中添加一个对象，但是因为 EqualExample 没有实现 hashCode() 方法，因此这两个对象的散列值是不同的，最终导致集合添加了两个等价的对象。
            EqualExample e1 = new EqualExample(1, 1, 1);
            EqualExample e2 = new EqualExample(1, 1, 1);
            System.out.println(e1.equals(e2)); // true
            HashSet<EqualExample> set = new HashSet<>();
            set.add(e1);
            set.add(e2);
            System.out.println(set.size());   // 2
    
        理想的散列函数应当具有均匀性，即不相等的对像应当均匀分不到所有可能的散列值上。这就要求了散列函数要把所有域的值都考虑进来，
        可以将每个域都当成 R 进制的某一位，然后组成一个 R 进制的整数。R 一般取 31，因为它是一个奇素数，如果是偶数的话，当出现乘法溢出，信息就会丢失，因为与 2 相乘相当于左移一位。
        
        一个数与 31 相乘可以转换成移位和减法：31*x == (x<<5)-x，编译器会自动进行这个优化。
            @Override
            public int hashCode() {
                int result = 17;
                result = 31 * result + x;
                result = 31 * result + y;
                result = 31 * result + z;
                return result;
            }
    
    toString()
        默认返回 ToStringExample@4554617c 这种形式，其中 @ 后面的数值为散列码的无符号十六进制表示。
    
    clone()
        1.cloneable
            clone() 是 Object 的 protected 方法，它不是 public，一个类不显示去重写 clone()，其他类就不能直接去调用该实例的 clone() 方法。
            
            未实现 Cloneable 接口调用 super.clone() 方法会抛异常：java.lang.CloneNotSupportedException: CloneExample
            应当注意的是，clone() 方法并不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。
            Cloneable 接口只是规定，一个类没有实现 Cloneable 接口又调用了 clone() 方法，就会包抛出 CloneNotSupportedException。
    
        2.浅拷贝
            拷贝对象和原始对象的引用类型引用同一个对象。
    
        3.深拷贝
            拷贝对象和原始对象的引用类型引用不同对象。
            
            实现深拷贝的三种方式：
                1.对象引用的引用也实现 Cloneable 接口，逐层实现，优点是可以定制，缺点是没有通用性
                2.基于反射，BeanUtil，Spring 核心包提供的一个工具类，基本原理就是获取class实例化，再通过反射整体复制属性
                3.基于 serialize，deserialize 实现，这种方法比较多，实际上本质上和反射没有太大差别，反射相当于 jvm 提供。
    
        
    
    
**关键字 final static**    
    
    final
        1.数据
            声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。
            对于基本类型，final 使数值不变；
            对于引用类型，final 使引用不变，也就不能引用其他对象，但是被引用的对象本身是可以修改的。

        2.方法
            声明方法不能被子类重写。
            private 方法隐式地被指定为 final，如果在子类中定义的方法和基类中的一个 private 方法相同，此时子类的方法不是重写基类方法，而是在子类中定义了一个新的方法。
    
        3.类
            声明类不允许被继承
    
    static
        1.静态变量
            静态变量：又称为类变量，也就是说这个变量属于类，类所有的实例都共享静态变量，可以直接通过类名来访问它；静态变量在内存中只存在一份。
            实例变量：每创建一个实例就会产生一个实例变量，它与该实例同生共死。
    
        2. 静态方法
            静态方法再类加载地时候就存在了，他不依赖于任何实例。所以静态方法必须有实现，也就是说他不能使抽象方法(abstract)。
            只能访问所属类的静态字段和静态方法，方法中不能又 this 和 super 关键字。
        
        3.静态语句块
            静态语句块在类初始化时运行一次。
         
        4.静态内部类
            非静态内部类依赖于外部类的实例，而静态内部类不需要。
                public class OuterClass {
                    class InnerClass{}
                    
                    static class staticInnerClass{}
                    
                    public static void main(String[] args){
                        // InnerClass innerClass = new InnerClass(); // 'OuterClass.this' cannot be referenced from a static context
                        OutClass outClass = new OuterClass();
                        InnerClass innerClass = outerClass.new InnerClass();
                        StaticInnerClass staticInnerClass = new StaticInnerClass();
                    }
                }
    
        5.静态导包
            在使用静态变量和方法时不用在指明 ClassName，从而简化代码，但可读性大大降低。
                import static com.xxx.ClassName.*;
    
        6.初始化顺序
            静态变量和静态语句优先于实例变量和普通语句块，静态变量和静态代码块地初始化顺序取决于它们在代码中的顺序。
                public static String staticField = "静态变量";
                
                static {
                    System.out.println("静态语句块");
                }
                
                public String field = "实例变量";
                
                {
                    System.out.println("普通语句块");
                }
                最后才是构造函数的初始化。
                public InitialOrderTest(){
                    System.out.println("构造函数");
                }
     
            存在继承的情况下，初始化顺序为：
                父类(静态变量、静态语句块)
                子类(静态变量、静态语句块)
                父类(构造函数)
                父类(实例变量、普通语句块)
                子类(构造函数)
    
    
   
   
**反射**

    每个类都有一个 Class 对象，包含了与类有关的信息。当编译一个新类时，会产生一个同名的 .class 文件，该文件内容保存着 Class 对象。
    
    类加载相当于 Class 对象的加载。类在第一次使用时才动态加载到 JVM 中，可以使用 Class.forName("com.mysql.jdbc.Driver") 这种方式来控制类的加载，该方法会返回一个 Class 对象。
    
    反射可以提供运行时的类信息，并且这个类可以在运行时才加载进来，甚至在编译时期该类的 .class 不存在也可以加载进来。
    
    Class 和 java.lang.reflect 一起对反射提供了支持，java.lang.reflect 类库主要包含了以下三个类：
    
    Field：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
    Method：可以使用 get() 和 set() 方法读取和修改 Field 对象关联的字段；
    Contructor：可以用 Constructor 创建新的对象。
    
    
    
**异常**    

    Throwable 可以用来表示任何可以作为异常抛出的类，分为两种：Error 和 Exception。其中 Error 用来表示 JVM 无法处理的错误，Exception 分为两种：
        受检异常：需要用 try...catch... 语句捕获并进行处理，并且可以从异常中恢复；
        非受检异常：是程序运行时错误，例如除 0 会引发 Arithmetic Exception，此时程序崩溃并且无法恢复。
    
    Object
        Throwable
            Exception
                RuntimeException
                    IllegalArgumentException
                        NumberFormatException
                    ArithmeticException
                    IndexOutOfBoundsException
                        ArrayIndexOutOfBoundsException
                IOException
                    FileNotFoundException
                    SocketException
            Error
                OutOfMemoryError
                LinkageError
                StackOverflowError
        
    
    
    
    
**泛型**    

    public class Box<T> {
        // T stands for "Type"
        private T t;
        public void set(T t) { this.t = t; }
        public T get() { return t; }
    }
             
    
    
**注解**    
    
    Java 注解是附加在代码中的一些元信息，用于一些工具在编译、运行时进行解析和使用，起到说明、配置的功能。注解不会也不能影响代码的实际逻辑，仅仅起到辅助性的作用。
    
    
        
**特性 Java 各版本的新特性 Java 与 C++ 的区别 JRE or JDK**    
    
    Java 各版本的新特性
        New highlights in Java SE 7
            1.Strings in Switch Statment (Switch语句支持字符串)
            2.Type Inference For Generic Instance Creation
            3.Multiple Exception Handling
            4.Support For Dynamic Languages
            5.Try with Resources
            6.Java nio Package
            7.Binary Literals, Underscore in literals
            8.Diamond Syntax
        
        New highlights in Java SE 8
            1.Lamdba Expressions
            2.Pipelines and Streams
            3.Date and Time API
            4.Default Methods
            5.Type Annotations
            6.Nashhorn JavaScript Engine
            7.Concurrent Accumulators
            8.Parallel operations
            9.PermGen Error Removed
    
    Java 与 C++ 的区别
        Java 是存粹的面向对象语言，所有的对象都继承自 java.lang.Object，C++ 为了兼容 C 即支持面向对象也支持面向过程。
        Java 是通过虚拟机从而实现跨平台特性，但是 C++ 依赖于特定的平台。
        Java 没有指针，它的引用可以理解为安全指针，而 C++ 具有和 C 一样的指针。
        Java 支持自动垃圾回收，而 C++ 需要手动回收。
        Java 不支持多重继承，只能通过实现多个接口来达到相同目的，而 C++ 支持多重继承。
        Java 不支持操作符重载，虽然可以对两个 String 对象支持加法运算，但是这是语言内置支持的操作，不属于操作符重载，而 C++ 可以。
        Java 的 goto 是保留字，但是不可用，C++ 可以使用 goto。
        Java 不支持条件编译，C++ 通过 #ifdef #ifndef 等预处理命令从而实现条件编译。
    
    JRE or JDK
        JRE 是 JVM 程序，Java 应用程序需要在 JRE 上运行。
        JDK 是 JVM 的超集，JRE 是用于开发 Java 程序的工具。例如，它提供了编译器 “javac”。
    