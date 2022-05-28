### 如何发现程序运行中与内存有关的错误？

叶佳辰 2020010178

#### 隐蔽又致命的错误

内存有关的错误有很多种，包括内存泄漏、内存移除、段错误等等。

当我们越界读写的时候，有时候程序运行会报错，有时候就不会，而是继续运行并给出一个奇怪的结果。有时候越界读写可能只是我们在写循环的时候多加了一个一的问题，很难发现。

此外，比如忘记写/多写了delete，或者写了个死循环导致不停new直至内存爆炸，都是硬看代码不容易发现的错误。

这时候就需要向大家介绍两个比较好用的辅助改正这些错误的工具。

####  $valgrind$的使用

##### $valgrind$是什么？

$Valgrind$是一套$Linux$下，开放源代码$（GPL V2）$的仿真调试工具的集合。$Valgrind$由内核（$core$）以及基于内核的其他调试工具组成。内核类似于一个框架$（framework）$，它模拟了一个$CPU$环境，并提供服务给其他工具；而其他工具则类似于插件$ (plug-in)$，利用内核提供的服务完成各种特定的内存调试任务。你可以在它的环境中运行你的程序来监视内存的使用情况。

##### $Valgrind工具包包含多个工具，如Memcheck,Cachegrind,Helgrind, Callgrind，Massif。$其体系结构如下图所示：

![image-20220527162020208](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162020208.png)

##### $valgrind$的安装

可以登录$valgrind$的官网https://valgrind.org/下载压缩包进行安装。

或者，直接$sudo apt install valgrind$
（可能需要根据命令行的相应提示添加相应参数）

![image-20220527162056116](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162056116.png)



##### $memcheck$

##### $Memcheck$ 工具主要检查下面的程序错误：

+ 使用未初始化的内存 
+ 使用已经释放了的内存 
+ 使用超过 $malloc$分配的内存空间
+ 对堆栈的非法访问 
+ 申请的空间是否有释放 
+ $malloc/free/new/delete$申请和释放内存的匹配
+ $src和dst$的重叠

使用方法：
$valgrind --tool=memcheck  --leak-check=full ./test1.exe（待检测的可执行文件）$

技巧：

在编译时添加$-g$参数，让$valgrind$能直接定位到出错代码所在的行数。

##### 一个小例子

![image-20220527162344321](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162344321.png)

![image-20220527162348582](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162348582.png)

![image-20220527162353554](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162353554.png)

![image-20220527162400074](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162400074.png)



#### 智能指针的使用

##### 智能指针是什么？

C++11 引入了 3 个智能指针类型：

+ $std::unique\_ptr<T>$ ：独占资源所有权的指针。
+ $std::shared\_ptr<T>$ ：共享资源所有权的指针。
+ $std::weak\_ptr<T>$ ：共享资源的观察者，需要和 std::shared_ptr 一起使用，不影响资源的生命周期。

人话版本：

+ $std::unique\_ptr<T> $：它指向的资源只能同时被一个指针指。
+ $std::shared\_ptr<T> $：它指向的资源可以同时被多个指针指。
+ $std::weak\_ptr<T> $：共享资源的观察者，需要和 std::shared_ptr 一起使用，不影响资源的生命周期。

##### $std::unique\_ptr<T>$

![](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162949673.png)

##### $std::shared\_ptr<T>$

![](C:\Users\yyj\AppData\Roaming\Typora\typora-user-images\image-20220527162955297.png)

##### $std::weak\_ptr<T>$

为什么介绍？其是依赖于$shared\_ptr$存在的。
$shared\_ptr$使用中的雷：
空悬指针： $the \ dangling\ pointer\ problem$
两个$shared\_ptr$指向同一个对象(object)，当对象以被某种方式销毁时，就会出现空悬指针的现象，即会出现指针指向的对象实际上不存在。
使用$weak\_ptr$即可避免这种问题。$weak\_ptr$不控制对象的生命期，但是它知道对象是否还活着。如果对象还活着，那么它可以提升为有效的$shared\_ptr$（提升操作通过lock()函数获取所管理对象的强引用指针）；如果对象已经死了，提升会失败，返回一个空$shared\_ptr$。