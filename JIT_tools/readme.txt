探索编译器
本节主要关注两个项目-numba和pypy,numba设计用于动态地编译小型函数，直接将pyhton函数编译成机器码。pypy是一款解释器，它在运行阶段对代码进行分析。并自动对速度缓慢循环进行优化。
这些工具都被称为JIT即时编译器，因为编译是在运行阶段而不是运行代码前进行的。代码前称为AOT预先编译器。
本章主要内容：
numba基础
使用原生模式编译实现快速函数
理解并实现通用函数
JIT类
安装PyPy
使用PyPy运行粒子模拟器
其他有趣编译器
一.numba基础
它在运行阶段使用低级虚拟机工具链（LLVM）对python函数进行编译。
LLVM是一组设计用于编写编译器的工具，并非针对特定语言，因此被众多的语言编写编译器。
LLVM的核心是中间表示，即LLVM IR，这是一种独立于平台的低级语言（类似汇编），可通过对其进行编译来生成特定平台上运行的机器码。
numba使用巧妙的类型猜测算法（类型推断），并通过编译包含类型信息的函数版本来提高执行速度。
开发numba旨在改善数值计算的代码的性能，因此重点是优化大量使用numpy数组的应用程序。
（1.numba入门）
如numba_demo1.py所示
二.使用原生模式编译实现函数
numba在处理非常简单的函数时的行为，numba表现十分出色，无论处理的是数组还是列表，性能都得到极大地提高。
numba能够在多大程度上进行优化取决于两个因素：能否准确判断变量的类型；能否将标准的python操作转换为速度更快的，针对特定类型的版本。。如果做到这两点，就能不使用解释器，获得
类似与cython的性能提升。
求助于解释器：对象模式，反之：原生模式。
inspect_types函数，可帮助了解类型推断的效果，以及哪些操作被优化。
详见numba_ys_obj.py
三.理解并实现通用函数
最初numba旨在提升使用numpy数组的代码的性能，numba高效的实现了许多numpy的功能
(1.numba通用函数)
通用函数是numpy中定义的特殊函数，可根据广播规则操作不同长度和shape的数组，numba快速实现了ufunc。(np.log就是一个通用函数)，另外接受多个参数的通用函数也根据广播规则进行工作，这样的同意函数包括
np.sum和np.difference,如numba_ufunc.py所示。numba能够进行类型推导，通用函数另一个特点是能够并行执行，向装饰器nb.vectorize传递关键字参数target='cpu' or'gpu'
(2.泛化通用函数)
通用函数的局限性是必须针对标量，泛化通用函数是对接受数组的过程的通用函数拓展，事例如numba_gufunc.py、
四.JIT类
numba不支持对泛型python对象进行优化，这种限制对数值计算代码影响不大，因此通常只涉及数组和数学运算。但是numba支持自定义类，而这些类可编译成快速的原生代码。
详见jit_custom_class.py
numba的局限性：
在某些情况下，numba无法正确的推断除变量的类型，进而拒绝编译，使用numba最好变量明确初始化。
五.PyPy项目
pypy旨在改进python解释器的性能，通过在运行阶段自动编译速度缓慢的代码。pypy使用Rpyhton编写(Restricted python),只实现了python语言中开发编译器的那一部分
策略：跟踪式JIT编译(tracing JIT compilation).在numba中，编译单元为方法和函数，而PyPy只专注于速度缓慢的循环。numba优化数值计算代码，PyPy致力于取代Cpython解释器
六.其他有趣的项目
Nuitka是Kay Hayen开发的一个程序，它将python代码编译成c代码，这些代码在性能上比Cpython高些，专注于与pyhton兼容
Pyston是Dropbox开发的一款解释器，用于支持JIT编译器，类似于numba。
