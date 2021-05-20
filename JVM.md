# JVM

## JVM内存模型

<img src="C:\Users\13523\Desktop\面试题\jvm内存模型.jpg" alt="jvm内存模型" style="zoom: 80%;" />

程序计数器：当前线程所执行的字节码的行号指示器，用于记录正在执行的虚拟机字节指令地址，线程私有。

Java虚拟栈：存放基本数据类型、对象的引用、方法出口等，线程私有。

Native方法栈：和虚拟栈相似，只不过它服务于Native方法，线程私有。

Java堆：java内存最大的一块，所有对象实例、数组都存放在java堆，GC回收的地方，线程共享。

方法区：存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码数据等。（即永久带），回收目标主要是常量池的回收和类型的卸载，各线程共享

## JVM中一次完整的GC流程是怎么样的，对象如何晋升到老年代

java堆=老年代+新生代

新生代=Eden区+SurvivorFrom区+SurvivorTo区

Eden区：Java新对象的出生地（如果新创建的对象占用内存很大，则直接分配到老年代如很长的字符串）。当Eden区内存不够的时候就会触发MinorGC，对新生代区进行一次垃圾回收。

SurvivorTo：保留了一次MinorGC过程中的幸存者。

SurvivorFrom：上一次GC的幸存者，作为这一次GC的被扫描者。

当JVM无法为新建对象分配内存空间的时候（Eden满了），MinorGC就会被触发，因此新生代空间占用率越高，MinorGC越频繁。

### MinorGC的过程：采用复制算法。

1. 首先，把Eden和SurvivorFrom区域中存活的对象复制到SurvivorTo区域（如果有对象的年龄以及达到了老年的标准，一般是15，则赋值到老年代区）
2. 同时把这些对象的年龄+1（如果SurvivorTo不够位置了就放到老年区）
3. 然后，清空Eden和SurvivorFrom中的对象；最后，SurvivorTo和SurvivorFrom互换，原SurvivorTo成为下一次GC时的SurvivorFrom区。

老年代

老年代的对象比较稳定，所以MajorGC不会频繁执行。

在进行MajorGC前一般都先进行了一次MinorGC，使得有新生代的对象晋身入老年代，导致空间不够用时才触发。当无法找到足够大的连续空间分配给新创建的较大对象时也会提前触发一次MajorGC进行垃圾回收腾出空间。

MajorGC采用标记—清除算法：

1. 首先扫描一次所有老年代，标记出存活的对象
2. 然后回收没有标记的对象。

MajorGC的耗时比较长，因为要扫描再回收。MajorGC会产生内存碎片，为了减少内存损耗，我们一般需要进行合并或者标记出来方便下次直接分配。

当老年代也满了装不下的时候，会进行Full GC，Full GC会 清理整个内存堆，如果老年代还是超出内存容量，就会抛出OOM（Out of Memory）异常。

### Major GC的触发机制：

　　Major GC又称为Full GC。当老年代空间不够用的时候，虚拟机会使用“标记—清除”或者“标记—整理”算法清理出连续的内存空间，分配对象使用。







```
单词目录
Eden[ˈiːdn]
Survivor[sərˈvaɪvər]
Minor[ˈmaɪnər]
Major[ˈmeɪdʒər]


```

