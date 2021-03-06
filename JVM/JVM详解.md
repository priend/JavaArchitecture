## 1、**JVM 的结构基本上由 4 部分组成：**
  - 类加载器，在 JVM 启动时或者类运行时将需要的 class 加载到 JVM 中
  - 执行引擎，执行引擎的任务是负责执行 class 文件中包含的字节码指令，相当于实际机器上的 CPU
  - 内存区，将内存划分成若干个区以模拟实际机器上的存储、记录和调度功能模块，如实际机器上的各种功能的寄存器或者 PC 指针的记录器等
  - 本地方法调用，调用 C 或 C++ 实现的本地方法的代码返回结果
![](https://www.icheesedu.com/images/qiniu/11.jpeg)

- 1、**Native Method Stack本地方法栈**

  - 它的具体做法是Native Method Stack中登记native方法，在Execution Engine执行时加载native libraies。

-  2、 **PC Register程序计数器**

  - 每个线程都有一个程序计算器，就是一个指针，指向方法区中的方法字节码（下一个将要执行的指令代码），由执行引擎读取下一条指令，是一个非常小的内存空间，几乎可以忽略不记。**记录当前线程所执行到的字节码的行号。**
  - 程序计数器是一块较小的内存空间，它可以看作是当前线程所执行的字节码的行号指示器。
  
  - 程序计数器处于线程独占区。
  - 如果线程执行的是 Java方法，这个计数器记录的是正在执行的虚拟机字节码指令的地址。如果正在执行的是native方法，这个计数器的值为undefined。
  - 此区域是唯一一个在java虚拟机规范中没有规定任何OutOfMemoryError情况的区域。

-  3、**Method Area方法区**

   - 方法区是被所有线程共享，所有字段和方法字节码，以及一些特殊方法如构造函数，接口代码也在此定义。简单说，所有定义的方法的信息都保存在该区域，此区域属于共享区间。
   
   - 存储虚拟机加载的类信息，常量，静态变量，即时编译器编译后的代码等数据。包括 类的版本，字段，方法，接口。
   - 静态变量+常量+类信息+运行时常量池存在方法区中，实例变量存在堆内存中。
   - 方法区和永久代  使用永久代来实现方法区
   - 垃圾回收在方法区的行为
   - 异常的定义

   - 运行时常量池：
       - 运行时常量池（Runtime Constant Pool），它是方法区的一部分。Class文件中除了有类的版本、字段、方法、接口等描述等信息外，还有一项信息是常量池（Constant Pool Table），用于存放编译期生成的各种字面量和符号引用，这部分内容将在类加载后存放到常量池中。
       
       - 运行时常量池相对于Class文件常量池的另外一个重要特征是具备**动态性**，Java语言并不要求常量一定只有编译期才会产生，也就是并非预置入Class文件中常量池的内容才能进入方法区运行时常量池，运行期间也可能将新的常量放入池中，这种特性被开发人员利用的比较多的便是String类的intern()方法。
- 4、 **Stack 栈：**
    - 栈也叫栈内存，主管Java程序的运行，是在线程创建时创建，它的生命期是跟随线程的生命期，线程结束栈内存也就释放，对于栈来说不存在垃圾回收问题，只要线程一结束该栈就Over，生命周期和线程一致，是线程私有的， 基本类型的变量和对象的**引用变量**都是在函数的栈内存中分配。遵循“先进后出”/“后进先出”原则。**存放方法运行时所需的数据，称为栈帧。**
    - 虚拟机栈描述的是Java方法执行的动态内存模型。
    
    - 栈帧
        - 每个方法执行，都会创建一个栈帧，伴随着方法从创建到执行完成。用于存储局部变量表，操作数栈，动态链接，方法出口等。
    - 局部变量表
        - 存放编译器可知的各种基本数据类型，引用类型，returnAddress类型
        - 局部变量表的内存空间在编译期完成分配，当进入一个方法时，这个方法需要在栈帧分配多少内存都是固定的，在方法运行期间时不会改变局部变量表的大小。
    - 大小 
       - 会出现stackOverflowError，栈内存溢出。
       - OutOfMemoryError ,内存溢出

- 5、**Heap 堆：**
    - 堆这块区域是JVM中最大的，应用的对象和数据都是存在这个区域，这块区域也是线程共享的，也是 gc 主要的回收区，一个 JVM 实例只存在一个堆内存，堆内存的大小是可以调节的。类加载器读取了类文件后，需要把类、方法、常变量放到堆内存中，以方便执行器执行。
    
    - 存放对象实例
    - 垃圾收集器管理的主要区域
    - 新生代，老年代，Eden空间。
    - OutOfMemoryError ,内存溢出

- 6、**成员变量，局部方法，静态变量之间的区别**
 
    |  | 成员变量 | 局部方法 | 静态变量 |
    | --- | --- | --- | --- |
    | 定义位置 | 在类中，方法外 | 方法中，或者方法的形式参数 | 在类中，方法外 |
    | 初始化值 | 有默认初始化值 | 无，先定义，赋值后才能使用 | 有默认初始化值 |
    | 调用方法 | 对象调用 | --------------------- | 对象调用，类名调用 |
    | 存储位置 | 堆中 | 栈中 | 方法区 |
    | 生命周期 | 与对象共存亡 | 与方法共存亡 | 与类共存亡 |
    | 别名 | 实例变量 | --------------------- | 类变量 |

## 2、对象在内存中的布局-对象的创建

![](https://www.icheesedu.com/images/qiniu/1111.png)


- 1.**检测类是否被加载：**
  - 当虚拟机执行到new时，会先去常量池中查找这个类的符号引用。如果能找到符号引用，说明此类已经被加载到方法区（方法区存储虚拟机已经加载的类的信息），可以继续执行；如果找不到符号引用，就会使用类加载器执行类的加载过程，类加载完成后继续执行。

- 2.**为对象分配内存：**
  - 类加载完成以后，虚拟机就开始为对象分配内存，此时所需内存的大小就已经确定了。只需要在堆上分配所需要的内存即可。
具体的分配内存有两种情况：第一种情况是内存空间绝对规整，第二种情况是内存空间是不连续的。
对于内存绝对规整的情况相对简单一些，虚拟机只需要在被占用的内存和可用空间之间移动指针即可，这种方式被称为指针碰撞。
  - 对于内存不规整的情况稍微复杂一点，这时候虚拟机需要维护一个列表，来记录哪些内存是可用的。分配内存的时候需要找到一个可用的内存空间，然后在列表上记录下已被分配，这种方式成为空闲列表。
  
  分配内存的时候也需要考虑线程安全问题，有两种解决方案

      - 第一种是采用同步的办法，使用CAS来保证操作的原子性。
      - 另一种是每个线程分配内存都在自己的空间内进行，即是每个线程都在堆中预先分配一小块内存，称为本地线程分配缓冲（TLAB），分配内存的时候再TLAB上分配，互不干扰。

- 3.**为分配的内存空间初始化零值：**
   - 对象的内存分配完成后，还需要将对象的内存空间都初始化为零值，这样能保证对象即使没有赋初值，也可以直接使用。

- 4.**对对象进行其他设置：**
   - 分配完内存空间，初始化零值之后，虚拟机还需要对对象进行其他必要的设置，设置的地方都在对象头中，包括这个对象所属的类，类的元数据信息，对象的hashcode，GC分代年龄等信息。

- 5.**执行 init 方法：**
   - 执行完上面的步骤之后，在虚拟机里这个对象就算创建成功了，但是对于Java程序来说还需要执行init方法才算真正的创建完成，因为这个时候对象只是被初始化零值了，还没有真正的去根据程序中的代码分配初始值，调用了init方法之后，这个对象才真正能使用。


## 3、对象的内存布局是怎样的

- 对象的内存布局包括三个部分：对象头，实例数据和对齐填充。
   - 对象头：对象头包括两部分信息，第一部分是存储对象自身的运行时数据，如哈希码，GC分代年龄，锁状态标志，线程持有的锁，偏向线程ID，偏向时间戳等。第二部分是类型指针，即对象指向类元数据的指针。
   
   - 实例数据：就是数据啦
   - 对齐填充：不是必然的存在，就是为了对齐的嘛

  
  ![](https://www.icheesedu.com/images/qiniu/285763-20170807175816862-1918148270.png)

## 4、对象是如何定位访问
- 对象的访问定位有两种：句柄定位和直接指针
  - 句柄定位：Java 堆会画出一块内存来作为句柄池，reference中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息
 
      ![](https://www.icheesedu.com/images/qiniu/232323.jpeg)
  
  - 直接指针访问：java堆对象的不居中就必须考虑如何放置访问类型数据的相关信息，而reference中存储的直接就是对象地址

     ![](https://www.icheesedu.com/images/qiniu/640-4.jpeg)
   
  比较：使用直接指针就是速度快，使用句柄reference指向稳定的句柄，对象被移动改变的也只是句柄中实例数据的指针，而reference本身并不需要修改。




