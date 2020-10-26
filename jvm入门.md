

# 1.引言

## 1.1什么是jvm?

定义：

Java Virtual Machine - java 程序的运行环境（java 二进制字节码的运行环境）

好处：

- 一次编写，到处运行
- 自动内存管理，垃圾回收功能
- 数组下标越界检查
- 多态

比较：

jvm jre jdk

![image-20201022152509190](/img/image-20201022152509190.png)



## 1.2学习jvm有什么用？

- 面试 提高竞争力
- 理解底层的实现原理
- 中高级程序员的必备技能

## 1.3常见的jvm

![image-20201022153451427](/img/image-20201022153451427.png)

## 1.4学习路线

![image-20201022154007216](/img/image-20201022154007216.png)



# 2.内存结构

## 2.1程序计数器



![image-20201022155233659](/img/image-20201022155233659.png)

### 2.1.1定义

Program Counter Register 程序计数器（寄存器）

- 作用，是记住下一条jvm指令的执行地址
- 特点
  - 是线程私有的
  - 不会存在内存溢出

```java
0: getstatic   #20          // PrintStream out = System.out;
3: astore_1               	// --
4: aload_1               // out.println(1);
5: iconst_1              // --
6: invokevirtual #26         // --
9: aload_1               // out.println(2);
10: iconst_2              // --
11: invokevirtual #26         // --
14: aload_1              // out.println(3);
15: iconst_3              // --
16: invokevirtual #26         // --
19: aload_1              // out.println(4);
20: iconst_4              // --
21: invokevirtual #26         // --
24: aload_1              // out.println(5);
25: iconst_5              // --
26: invokevirtual #26         // --
29: return
```



## 2.2虚拟机栈



![image-20201022150526932](/img/image-20201022150526932.png)

### 2.2.1定义

Java Virtual Machine Stacks （Java 虚拟机栈）

- 每个线程运行时所需要的内存，称为虚拟机栈
- 每个栈由多个栈帧（Frame）组成，对应着每次方法调用时所占用的内存
- 每个线程只能有一个活动栈帧，对应着当前正在执行的那个方法



问题辨析
1. 垃圾回收是否涉及栈内存？

   不需要  栈内存在方法调用后就会被弹出

2. 栈内存分配越大越好吗？

   不一定 增加大了反而会影响线程的数目

3. 方法内的局部变量是否线程安全？
    如果方法内局部变量没有逃离方法的作用访问（线程私有的），它是线程安全的
    如果是局部变量引用了对象，并逃离方法的作用范围（共享的），需要考虑线程安全



### 2.2.2栈内存溢出



- 栈帧过多导致栈内存溢出（方法递归调用可能导致栈帧过多）
- 栈帧过大（不太容易出现）



### 2.2.3线程运行诊断

案例1： cpu 占用过多
定位

- 用top定位哪个进程对cpu的占用过高
- ps H -eo pid,tid,%cpu | grep 进程id （用ps命令进一步定位是哪个线程引起的cpu占用过高）
- jstack 进程id
  - 可以根据线程id 找到有问题的线程，进一步定位到问题代码的源码行号

案例2：程序运行很长时间没有结果

​	有可能是多个线程之间死锁



## 2.3本地方法栈



![image-20201022155309893](/img/image-20201022155309893.png)

需要调用操作系统的底层方法的内存都在本地方发栈



## 2.4堆

![image-20201022185841069](/img/image-20201022185841069.png)

### 2.4.1定义

Heap 堆

- 通过 new 关键字，创建对象都会使用堆内存

特点

- 它是线程共享的，堆中对象都需要考虑线程安全的问题
  有垃圾回收机制

### 2.4.2堆内存溢出



```java
**
 * 演示堆内存溢出 java.lang.OutOfMemoryError: Java heap space
 * -Xmx8m
 */
public class Demo1_5 {

    public static void main(String[] args) {
        int i = 0;
        try {
            List<String> list = new ArrayList<>();
            String a = "hello";
            while (true) {
                list.add(a); // hello, hellohello, hellohellohellohello ...
                a = a + a;  // hellohellohellohello
                i++;
            }
        } catch (Throwable e) {
            e.printStackTrace();
            System.out.println(i);
        }
    }
}
```



### 2.4.3堆内存诊断

1. jps 工具
    查看当前系统中有哪些 java 进程
2. jmap 工具
    查看堆内存占用情况 jmap - heap 进程id (只能查看某一时刻)
3. jconsole 工具
    图形界面的，多功能的监测工具，可以连续监测

## 2.5方法区

![image-20201022202044845](/img/image-20201022202044845.png)

### 2.5.1定义

Java虚拟机具有一个在所有Java虚拟机线程之间共享的*方法区域*。该方法区域类似于常规语言的编译代码的存储区域，或者类似于操作系统过程中的“文本”段。它存储每个类的结构，例如运行时常量池，字段和方法数据，以及方法和构造函数的代码，包括用于类和实例初始化以及接口初始化的特殊方法（[2.9节](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.9)）。

方法区域是在虚拟机启动时创建的。尽管方法区域在逻辑上是堆的一部分，但是简单的实现可以选择不进行垃圾回收或压缩。该规范没有规定方法区域的位置或用于管理已编译代码的策略。方法区域可以是固定大小的，或者可以根据计算的需要进行扩展，如果不需要更大的方法区域，则可以缩小。方法区域的内存不必是连续的。

[虚拟机规范](https://docs.oracle.com/javase/specs/jvms/se8/html/index.html)https://docs.oracle.com/javase/specs/jvms/se8/html/index.html



### 2.5.2组成

<img src="/img/image-20201023000621425.png" alt="image-20201023000621425" style="zoom:80%;" />



### 2.5.3方法区内存溢出



溢出代码demo

jvm1.8  1.8 之后会导致元空间内存溢出

```java
/**
 * 演示元空间内存溢出 java.lang.OutOfMemoryError: Metaspace
 * -XX:MaxMetaspaceSize=8m
 */
public class Demo1_8 extends ClassLoader { // 可以用来加载类的二进制字节码
    public static void main(String[] args) {
        int j = 0;
        try {
            Demo1_8 test = new Demo1_8();
            for (int i = 0; i < 10000; i++, j++) {
                // ClassWriter 作用是生成类的二进制字节码
                ClassWriter cw = new ClassWriter(0);
                // 版本号， public， 类名, 包名, 父类， 接口
                cw.visit(Opcodes.V1_8, Opcodes.ACC_PUBLIC, "Class" + i, null, "java/lang/Object", null);
                // 返回 byte[]
                byte[] code = cw.toByteArray();
                // 执行了类的加载
                test.defineClass("Class" + i, code, 0, code.length); // Class 对象
            }
        } finally {
            System.out.println(j);
        }
    }
}

```

jvm1.6  1.8 以前会导致永久代内存溢出

```java
/**
 * 演示永久代内存溢出  java.lang.OutOfMemoryError: PermGen space
 * -XX:MaxPermSize=8m
 */
public class Demo1_8 extends ClassLoader {
    public static void main(String[] args) {
        int j = 0;
        try {
            Demo1_8 test = new Demo1_8();
            for (int i = 0; i < 20000; i++, j++) {
                ClassWriter cw = new ClassWriter(0);
                cw.visit(Opcodes.V1_6, Opcodes.ACC_PUBLIC, "Class" + i, null, "java/lang/Object", null);
                byte[] code = cw.toByteArray();
                test.defineClass("Class" + i, code, 0, code.length);
            }
        } finally {
            System.out.println(j);
        }
    }
}

```



场景
mybatis  spring  等框架采用可能会导致内存溢出(cglib)

### 2.5.4运行时常量池

- 常量池，就是一张表，虚拟机指令根据这张常量表找到要执行的类名、方法名、参数类型、字面量
  等信息
- 运行时常量池，常量池是 *.class 文件中的，当该类被加载，它的常量池信息就会放入运行时常量
  池，并把里面的符号地址变为真实地址



### 2.5.5 StringTable

几道面试题

```java
String s1 = "a";
String s2 = "b";
String s3 = "a" + "b";
String s4 = s1 + s2;
String s5 = "ab";
String s6 = s4.intern();
// 问
System.out.println(s3 == s4);
System.out.println(s3 == s5);
System.out.println(s3 == s6);
String x2 = new String("c") + new String("d");
String x1 = "cd";
x2.intern();
// 问，如果调换了【最后两行代码】的位置呢，如果是jdk1.6呢
System.out.println(x1 == x2);
```

