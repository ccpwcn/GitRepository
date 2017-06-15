```shell
# custom IntelliJ IDEA VM options
# 官方推荐的修改方法是通过IDE的Help菜单的Edit Custom VM Options命令进行，点击此菜单命令会创建一个.vmoptions文件的副本，并且在IDE中打开供用户编辑
# VM Options将会从安装目录的bin\idea.exe或idea64.exe.vmoptions文件中加载，或者从配置目录（如果存在的话）中的这个文件的副本执行加载
# 需要注意的是配置目录中的.vmoptions文件的加载优先权高于bin目录中的.vmoptions。

# 如果运行的是基于64位JDK的launcher，那么加载的文件就是idea64.exe.vmoptions，如果运行的是32位的JDK的launcher，那么加载的文件就是idea.exe.vmoptions
# 然而在MacOS系统上，始终是idea.vmoptions

# 定义的原则是每行一个配置项

# 指定使用Server VM，这样初始堆空间会大一些，并且默认使用并发垃圾回收器。
# JVM在启动的时候会根据硬件和操作系统会自动选择使用Server还是Client类型的JVM。而在64位机器上只有Server类型的JVM
-server

# 设置Java堆的初始大小，也是最小堆的大小
-Xms8192m
# 限定JVM可以分配的最大堆大小
-Xmx8192m
# 设置年轻代内存区域的大小
-Xmn2048m

# 减少线程栈的大小，这样可以使剩余的系统内存支持更多的线程；
-Xss128k

# 针对JDK8的元数据空间初始大小，用于替代JDK7的-XX:PermSize选项（这是用来设置永久代内存空间大小的）
-XX:MetaspaceSize=512m
# 针对JDK8的元数据空间最大值，用于替代JDK7的-XX:MaxPermSize选项（这是用来设置永久代内存空间大小的）
-XX:MaxMetaspaceSize=4096m

# 默认开启回收线程数，和CPU*数量一样就好了
-XX:ParallelGCThreads=8

# 年轻代使用并行回收器
–XX:+UseParNewGC

# 代码缓存，它是用来存储已经编译成本地代码的内存空间，
-XX:InitialCodeCacheSize=512m
-XX:ReservedCodeCacheSize=1024m
# 这个选项的作用是：当代码缓存被填满时让JVM放弃一些编译代码。为什么会有这么一个不常用的选项？？？
# 因为如果代码缓存不断增长，例如，因为热部署引起了内存泄漏，最终代码缓存被占满时，JVM会打印一条警告信息，并且切换到interpreted-only模式，
# 在这种模式下，JIT编译器停用、字节码将不会被编译为机器码，虽然应用程序会继续运行，但是运行速度和性能会大幅度降低，更糟糕的，出现这种情况很难被发现，直到我们显著的发现应用程序运行比以前慢了很多。
# 结论就是：设置较大的代码缓存或者提高代码的缓存大小，将会延缓代码缓存区域发生内存溢出。
# 使用这个参数，我们可以避免当代码缓存被填满的时候JVM切换到interpreted-only 模式。
# 不过，最根本的解决问题的办法仍然是尽快找到代码缓存溢出的的根本原因，如找出内存泄漏所在并修复它。
-XX:+UseCodeCacheFlushing

# OOPS的意思是"Ordinary" object pointers，这个参数的含义是指定是否使用指针压缩，CompressedOops这个选项是指Java堆的对象指针的压缩，它并不包括类数据，UseCompressedClassPointers这个选项是JDK8中才有的，它特定对于JDK8的元数据空间（也就是Metaspace）影响JVM的行为，因此，如果您使用的是JDK7，那么您应当将此选项替换为：UseCompressedOops，如果您使用的是JDK8，那么建议使用此选项。
-XX:+UseCompressedClassPointers
# 指针压缩空间大小，启用指针压缩的情况下，此选项才有意义，并且这个值在JVM启动时就固定了，所以可以设置的大一点
-XX:CompressedClassSpaceSize=2048m

# 在内存出现OOM的时候，把Heap转存(Dump)到文件以便后续分析，文件名通常是java_pid<pid>.hprof，其中pid为该程序的进程号。
-XX:+HeapDumpOnOutOfMemoryError
# 用来指定heap转存文件的存储路径，需要指定的路径下有足够的空间来保存转存文件。
-XX:HeapDumpPath=E:/tmp/jvm_dump:
```
