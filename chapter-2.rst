第2章：简介
===========

本章将对C++进行一个整体介绍。本文档是由Frank编写，作为格罗宁根大学的教学课程。本文当并不是C++参考手册，并未涵盖C语言相关背景知识。可以参考其他的教程（例如the Dutch book De programmeertaal C, Brokken and Kubat, University of Groningen, 1996）或 `在线电子书 <http://publications.gbdirect.co.uk/c_book/>`_ 。

阅读本文档之前，您应该熟悉C语言编程。C++注解接着C语言继续深入，例如指针，基本流程控制和函数构造。

本文当并未涵盖一些基本的语法元素，因为这些符号（例如，用<:代表[，用>:代表]）在C++代码中基本不会出现。此外，三字母符号（用??<代表{，用??>代表}）在C++17标准里已经移除。

C++注解的版本号（当前是10.8.1）随着内容的更新而更新。第一个数字是主版本号，在之后的时间内基本不会发生变化，代表重写。中间的数字随着新的信息加入到本文档而增加。最后一个数字代表小改动，例如改正一些拼写错误。

本文档由格罗宁根大学信息中心基于 `GNU General Public License <http://www.gnu.org/licenses/>`_ 发布。

C++注解采用 `Yodl <https://fbb-git.github.io/yodl/>`_ 格式化系统。

对于本文当的所有的建议，改进和变更，请直接联系该作者：

    | Frank B. Brokken
    | Center of Information Technology,
    | University of Groningen
    | Nettelbosje 1,
    | P.O. Box 11044,
    | 9700 CA Groningen
    | The Netherlands
    | (email: f.b.brokken@rug.nl) 

本章主要是C++提供的特性的概览。包括一些一些对C的拓展，基于对象和面向对象的概念。

2.1：C++注解新增内容
--------------------

当第一个或第二个版本号发生变化时（偶尔也包括第三个版本号变化时），会修改此节的内容。主版本号升级，前一个版本保留，旧版本的入口被移除。

* 10.8.0版增加了一个新的章节（ `2.5.14`_ ），主要介绍C++17标准中引入的新特性，修正许多拼写错误和不确切的表述。同时，在C++17中移除的binders，negators和random_shuffle也被移除( coverage of (obsolete) binders, negators and random_shuffle, removed by the C++17 standard, was discontinued)。

* 10.7.2版对 `11.6 <chapter-11.rst#overloadding>`_ 节中重载和添加类的二元操作符部分进行了重写。

* 10.7.0版添加了关于C++17新标准的的相关章节（ `2.5.14`_ ）。此外，关于重载二元操作符和添加二元操作符到已有类的章节被重写

* 10.6.0版新加了一节（左值，右值等）介绍一些除了我们所熟知左值和右值之外的其他术语；添加了新的一节（标准异常：用还是不用），关于标准异常和非标准异常的区别；介绍如何使用new Type[size]()初始化一个已经分配龄的值/POD对象数组；the section about explicit conversion operators received an overhaul. 

* 10.5.0版包含一部分特定时间相关的工具。包括std::localtime，std::gmtime和std::put_time（ `20.1.5 <chapter-20.rst#time>`_ ），和put_time所能识别的说明符的完整表格。

* 10.4.0版添加之前丢失的关于引用绑定（ `11.6.1 <chapter-11.rst#refbind>`_ ）的章节。该节是11.6的子章节，涵盖重载二元操作符，和之后的一节，关于静态多态（ `22.12 <chapter-22.rst#staticpoly>`_ ）的一节完全重写。此外，C++注解的html版本的外观可以通过向cplusplus.css添加CSS元素进行调整。

* 10.3.0版包含几个关于表达式模板的章节，关于tributes，关于共享互斥锁，关于共享锁，关于各种查找，关于固定大小的存储单元分配函数，和关于移动和交换流；重新组织lambda表达式章节；添加额外的C++14标准引用。

* 10.2.0包含stl正则表达式类的重写，介绍std::placeholder命名空间和通用绑定器。由于C++11是当时的标准，明确提到它已被移除。C++14中提到的被保留，添加龄一些关于C++17标准的注释。

* 10.1.0版添加了关于C++11中正则表达式的处理，描述正则表达式处理的内容重容器一章中移动到标准模板库一章中。

* 10.0.0版添加关于多线程的一章，这是被C++11标准正式支持的。此外，添加了一些次要话题（如std::distance，一些新的语法元素）到C++注解。

2.2：C++历史
------------

C++的第一版实现是1980年左右在贝尔实验室，Unix操作系统诞生的地方。

C++原来是一个预编译器，类似于C语言中的预处理器，将代码中的特殊结构转换为纯C代码。然后由标准C编译器编译该代码。由C++预处理器读取的“预代码”通常以.cc，.C或.cpp作为文件扩展名。然后该文件被转换为以.c为扩展名的C代码，然后进行编译和链接。

C++源码文件的命名方式沿用下来：.cc和.cpp仍然在使用。然而，C++预处理器的工作现在基本上都是在真正的编译过程中处理了。通常编译器根据扩展名判断源码文件所使用的编写语言。Borland和Microsoft C++编译器假定C++源文件以.cpp为扩展名。Gnu g++编译器假定C++源文件以.cc为扩展名。

从过去把C++代码转换成C代码进行编译可以看出，C++是C的超集：C++提供完整的C语法，支持所有的C库函数，并且添加了独有的特性。这使得从C转换到C++相当容易。C程序员可以先从把源文件扩展名由.c改为.cc或.cpp开始C++编程，然后逐渐使用C++所提供的各种特性。不会有很大习惯上的改变。

2.2.1：C++注解的历史
````````````````````

C++注解的原始版本由Frank Brokken和Karel Kubat用荷兰语采用LaTex编写。一段时间后，1994年，karel用英语进行了重写，并转换到更合适的格式。

该教程的第一个版本在1994年10月首次在网上出现。然后，就被转换为SGML格式。

新章节不断的添加进来，内容不断的更新和完善（感谢无数给我发送他们评论的读者）。

在第四个主板本，Frank添加新的章节，并将该文档由SGML转换到yodl。

    C++注解可以自由分发。请确定阅读 `法律声明 <legal.rst>`_ 。

    **继续阅读本文当的后续部分，代表你已知晓并且同意该声明。**

如果你喜欢本文档，分享给你的朋友。更好的是，请给 `Frank <mailto:f.b.brokken@rug.nl)>`_ 发送电子邮件，让我们也知道。

2.2.2：用C++编译器编译C程序
```````````````````````````

潜在的C++程序员应该意识到C++并不是C的完美超集。当你重命名一个.c文件到.cc文件，并由C++编译器进行编译时，你可能会遇到一些不同的地方：

* 在C语言中，sizeof('c')和sizeof(int)相等，'c'可以使任意的ASCII字符。这里面的哲学可能是因为，当字符作为参数传递给函数是，是作为证书传递的。此外，C编译器处理像'c'这样的字符常量，是作为证书常量处理的。因此，在C中，这个函数调用

  ::

    putchar(10);

  和

  ::

    putchar('\n');

  是相同的。

  相比之下，在C++中，sizeof('c')总是1（但也得参考 `3.4.2 <chapter-3.rst#wchar>`_ ）。int总是int。后面我们将会看到，下面这两个函数调用

  ::

    somefunc(10);

  和

  ::

    somefunc('\n');

  可能会不同的函数处理：C++区别两个函数不仅仅判断它们的名字，也比较它们的参数类型。这两个函数中，参数就不一样，前者是整形参数，后者是字符型参数。

* C++需要非常严格的外部函数原型定义。例如，在C语言中，原型定义可能像这样

  ::

    void func();

  意为一个func()函数存在，没有返回值。函数声明没有指定可以接收什么类型的参数。

  然而，在C++中，上面的声明意味着该函数根本不接收任何参数。给它传递任何参数都会导致编译错误。

  注意，在声明函数的时候，extern关键字不是必要的。一个函数定义变为一个函数声明，只需要把函数体部分用分号代替即可。但是，在声明外部变量的时候，exterm关键字是必要的。

2.2.3：编译C++程序
``````````````````

要编译C++程序，得需要一个编译器。考虑到本文档是免费特性，自然不会感到吃惊，在这里会推荐一个免费编译器。http://www.gnu.org 的Free Software Foundation（FSF）已经提供了一个C++编译器，在其他的地方也可获取到，它也是Debian（ http://www.debian.org ） linux（ http://www.linux.org ）发行版中的一部分。

要使用C++14标准所提供的特性，需要提供--std=c++14的标识。在C++注解中，在编译示例代码时，总是假定使用了这个标识。要使用C++17标准中的特性，可以使用Gnu g++-6编译器。要使用C++17标准，要提供--std=c++1z标识。

2.2.3.1：MS-Windows下的C++
##########################

对于微软的windows系统，可以在Cygnus（ http://sources.redhat.com/cygwin ）上安装windows版本的Gnu g++编译器。

点击上面的链接后，通过点击现在安装，来获取一个免费的g++编译器。将会下载setup.exe，点击运行即可安装cygwin。该软件可以通过在网上下载setup.exe进行安装。也可以通过其他方式（如用CD-ROM），可参考Cygwin主页上的描述。安装过程是交互式的。按照默认的选项进行安装即可，除非你有特别的需求。

最新版本的Gnu g++编译器可以在 http://gcc.gnu.org 获取。如果Cygnus发行版提供的编译器不是最新版，可以通过下载最新版本的源代码进行编译安装，前提是已经有了一个可用的编译器。编译器的主页里有详细的安装步骤。根据我们的经验，在Cygnus环境下编译一个新的编译器是没有任何问题的。

2.2.3.2：编译C++源代码
######################

通常，你可以使用下面的命令编译C++源码文件“source.cc”:

::

  g++ source.cc

会产生一个可执行程序（a.out或a.exe）。如果默认的名称不合适，可以通过-o选项制定一个名称（下面命令产生一个叫做source的程序）：

::

  g++-o source source.cc

如果只需要产生汇编文件，可以通过-c选项来产生该模块：

::

  g++ -c source.cc

这会生成source.o，之后可被其他模块链接。如前面所指出，使用-std=c++14将激活C++14标准所包含的特性。

C++程序可能很快就会变得难以手工维护。所有的正式程序开发项目都使用工具进行维护。通常使用make程序维护C++程序，也有其他不错的选择，如icmake或ccbuild程序。

强烈将以在开始学习C++时就使用维护工具。

2.3：C++：优势和声称
--------------------

经常说用C++编程能产生“较好”的程序。一些C++声称的优势有：

* 新程序可以用更多的时间完成开发，因为可以复用之前的代码。
* 创建和使用新的数据类型要比C语言容易。
* C++的内存管理更加的简单和透明。
* 程序的bug变少，因为C++采用更严格的语法和类型检查。
* “数据隐藏”，一个程序中某部分使用的数据对于程序中的其他部分不可见，用C++更容易实现

上面这些宣称哪些是真的？最初，我们对C++的印象是，它有点高估自己；对于整个面向对象编程的方法也是如此。对C++的热情就像曾经对人工智能语言，如Lisp和Prolog一样：这些语言是设想用来毫不费力地解决最困难的人工智能问题的。新语言总是过度吹嘘：最终，每个问题都可以用任何语言编码（比如说BASIC或汇编语言）。对于一门语言，它的优势和劣势不是你可以用它们来做什么，而是更愿意去用哪种语言来高效和易懂的方案去解决一个问题。通常这些工具有语法上的限制，强制或推荐特定的结构或，仅通过应用或“拥抱”这样的语法形式来表示意图。与其用一大长串的纯汇编指令，我们现在使用流程控制语句，函数，对象，甚至模板（C++）来结构化和组织代码，来表达某个人用它选择的语言表达他自己的想法。

对于上述C++的宣称，我们支持如下。

* 开发新程序时复用现有的代码，C语言也可以做到，如使用函数库。函数可以集合到一个库里，不需要在每个新的程序里都重新编写。但是，C++提供了语法级的代码复用，而不是函数库（见 `13 <chapter-13.rst>`_ 和 `21 <chapter-21.rst>`_ ）。

* 创建和使用新的数据类型，在C中也可以，如使用struct，typedef等。其他结构可以从这些结构集成，从而实现struct包含struct等。在C++中，这些工具可以通过定义自包含的数据类型扩展，自动管理他们的内存（不需要凭借单独的系统内存管理系统，如java）。

* C++的内存管理原则上可以相当容易，但也可能想C一样困难。尤其是当C专用的函数，例如使用xmalloc和xrealloc函数（分配内存或当内存耗尽时终止程序）。然而，用例如malloc这样的函数，容易犯错。C中经常出现的错误，经过跟踪后发现都是使用malloc时的错误判断草成的。相比之下，C++提供了较安全的方式来申请内存，用new操作符。

* 关于bug多发，我们可以说C++实际上采用比C更严格的类型检查。但是，大多数现代C编译器都实现了警告级别；通常是开发者自己的选择去忽略或者修复警告。在C++中，许多这样的警告都称为了严重的错误（编译停止进行）。

* 关于数据隐藏，C提供了一些工具。例如，可以使用局部变量和静态变量，特定的类型，如结构体可以由专门的函数进行操作。使用这种技巧，C也可以实现数据隐藏；但是必须得承认，C++提供了特定的语法结构，更容易实现数据隐藏（通常叫封装）。

C++是一个门特别的语言，但并不是所有编程问题的解决方案。但是C++提供了新的，优雅的值得研究的设施（facilities）。不好的是，相比于C语言，C++语法层面的复杂程度是非常显著的。可以认为是这门语言的缺点。尽管，我们随着时间慢慢的习惯这些复杂度的增长，转换既不迅速也不痛苦。

通过C++注解，我们希望帮助C开发这转换到C++开发，专注于C++中比C多的功能，不去讲C的东西。我们希望你能够喜欢本文档，并能够从中受益。

享受C++的旅程，祝你好运。

_`2.4：什么是面向对象编程？`
-------------------------

面向对象（和基于对象）编程提供了一个与C编程所使用的模型稍微不同的方法。在C中，解决问题用“过程方法”：一个问题分解为自问体，然后重复分解，直到子任务可以进行编码。因此，要创建一大堆函数，彼此通过参数或变量（全局，局部或静态）交互。

相比之下（或此外），基于对象的方法标识问题中所使用的 **关键字** 。然后在途中描述这些关键字，他们之间用箭头来描述内部的层次结构。在实现中，关键字称为对象，层级定义对象之间的关系。这里用对象来描述一个有限的，自定义的结构，包含有关于一个实体的所有信息：数据类型和操作这些数据的函数。做为面向对象方法的一个例子，下面是一个示例：

  一个汽车经销商的员工和雇主和一个自动垃圾回收的公司按下列方式支付工资。首先，垃圾回收公司的体力劳动者每个支付一定数额的工资。然后，公司的拥有者每个月有固定的收入。然后，汽车销售人员每个月有固定的工资和每卖一辆车的提成。最后，该公司雇佣了四处旅行的二手车购买者；这些雇员每月领取工资，每买一辆汽车奖金，并归还旅行费用。

待续。。。

2.5：C和C++的不同
-----------------

本节会有一些C++示例代码。C和C++的不同部分会高亮显示。

2.5.1：main函数
```````````````

C++中，main函数只有两种形式：int main()和int main(int argc, char \*\*argv)。

注释：

* main的返回类型是int，不适void
* main函数不能被重载（除了上面说的两种形式）
* main函数结尾不需要明确的返回语句。如果省略，则返回0.
* argv[argc]的值是0
* C++标准没有定义第三个char \*\*envp参数，并且应该禁止使用。而是使用全局变量extern char \*\*environ，该变量应该是已经声明了的，用来访问程序的环境变量。它的最后一个值是0.
* 当main函数返回，C++程序正常结束。在main函数中使用try（ `10.11 <chapter-10.rst#funtry>`_ ）同样被认为是程序正常结束。当程序正常结束时，全局变量的析构函数（ `9.2 <chapter-2.rst#destructor>`_ 节）激活。像使用exit(3)这样的函数不会正常的结束C++程序，因此这类函数是废弃的。

2.5.2：行末注释
```````````````

根据ANSI/ISO的定义，C++实现了行末注释。注释以//开始，到行结束标记结束。标准的C注释，/\*和\*/也可以在C++中使用：

::

    int main()
    {
        // this is end-of-line comment
        // one comment per line

        /*
            this is standard-C comment, covering
            multiple lines
        */
    }

尽管上面的例子中使用了，但建议不要在C++函数体内使用C风格的注释。有时，为了测试，现有代码必须要临时禁用。在这种情况下，非常适合使用C风格的注释。如果这样的禁用的代码之间也包含此类注释，会产生嵌套的注释行，导致编译错误。因此，法则就是不要在C++函数体内使用C风格注释（除此之外，也可以使用#if 0和#endif预处理宏指令）。

2.5.3：严格的类型检查
`````````````````````

C++使用非常严格的类型检查。在函数被调用前，必须要知道它的原型，并且调用必须和原型一致。这个程序

::

  int main()
  {
      printf("Hello World\n");
  }

经常在C中编译，会有一个警告，printf()是未知函数。但是C++编译器会产生错误。错误原因是没有包含#include <stdio.h>（C++中更通常使用的#include <cstdio>）。

我们已经提到：C++ main函数总是使用int返回值，尽管可以定义个int main()函数不写return语句，但是在main函数中却不能使用一个没有int表达式的return语句。例如：

::

  int main()
  {
      return;     // won't compile: expects int expression, e.g.
                  // return 1;
  }

_`2.5.4：函数重载`
``````````````````

C++中可以定义相同名称的函数，但却执行不同的动作。这些函数的参数列表必须不一样（并且/或者它们的const属性）。示例如下：

::

  #include <stdio.h>

  void show(int val)
  {
      printf("Integer: %d\n", val);
  }

  void show(double val)
  {
      printf("Double: %lf\n", val);
  }

  void show(char const *val)
  {
      printf("String: %s\n", val);
  }

  int main()
  {
      show(12);
      show(3.1415);
      show("Hello World!\n");
  }

上面的代码中定义了三个show函数，只有参数列表不同，分别为int，double和char \*参数。这几个函数拥有相同的名字。拥有相同名字但不同参数列表的函数叫做重载。定义这种函数的行为叫做函数重载。

C++编译器采用简单的方式实现函数重载。尽管函数共享它们的名字（如上例），编译器（还有之后的连接器）使用不同的名字。从源文件中到内部使用的名字的转换称为“名称改编”。例如，C++编译器可能会把void show(int)函数的名称转换为内部使用的VshowI名称，类似的，char \*参数类型的函数转换为VshowCP。实际内部使用的名称是由编译器决定的，并且和开发者并不相关，除了在列出一个库中内容的时候。

关于函数重载的一些附加说明：

* 对于完成概念上不同任务的函数，不要使用函数重载。上面的例子中，各个show函数是有关系的（他们向屏幕打印信息）。
* C++不允许只有返回类型不同的同名函数，因为这总是由开发者决定是否使用一个函数的返回值，例如，该代码

::

  printf("Hello World!\n");

并未提供printf函数的返回值信息。两个只有返回值不同的printf函数对于编译器来说并无区别。

* 第 `7 <chapter-7.rst#classes>`_ 章，将会引入成员函数的概念( `7.7 <chapter-7.rst#constfunctions>`_ 节)。这里仅简单的提及，类通常有成员函数（见第 `5 <chatper-5.rst#string>`_ 章中中概念介绍）。除了能够使用不同的参数列表重载成员函数，还可以通过const属性重载成员函数。这种抢矿下，类可能有一对名称和参数列表完全相同的成员函数。此时，这些函数通过const属性重载。这种情况。这些函数中，必须有一个函数有const属性。


2.5.5：默认函数参数
```````````````````

C++中，定义函数时可以给函数提供默认参数。当开发者没有提供参数时，编译器会提供默认参数。例如：

::

  #include <stdio.h>

  void showstring(char *str = "Hello World!\n");

  int main()
  {
      showstring("Here's an explicit argument.\n");

      showstring();           // in fact this says:
                              // showstring("Hello World!\n");
  } 

在省略参数时，默认参数会被定义是一个很好的点：编译器提供趋势的参数，除非函数调用时明确指定。当默认参数被使用时，程序代码既不会变得更短，程序也不会变得更高效。

定义函数时，可以定义多个默认参数：

::

  void two_ints(int a = 1, int b = 4);

  int main()
  {
      two_ints();            // arguments:  1, 4
      two_ints(20);          // arguments: 20, 4
      two_ints(20, 5);       // arguments: 20, 5
  }

当函数two_ints被调用时，必要时，编译器会提供一个或两个参数。但想这样的语句two_ints(,6)是不允许的：当参数省略时，它们必须在右侧。

默认参数必须要在编译时是已知的，就是在那时提供的默认参数。因此，默认参数必须在函数声明时就给出，而不是在实现的时候。

::

  // sample header file
  extern void two_ints(int a = 1, int b = 4);

  // code of function in, say, two.cc
  void two_ints(int a, int b)
  {
      ...
  }

在函数定义时提供默认参数是错误的。当函数被其他源文件使用时，编译器读取头文件，而不是函数的定义。所以编译器无法得知默认参数的值。当前，如果编译器检测到在函数定义是提供默认参数会报错。

2.5.6：NULL指针、0指针和nullptr
```````````````````````````````

在C++中，所有的零值都编码为0。在C中，NULL通常在指针的上下文中使用。这种差异纯粹是文体上的，尽管被广泛采用。C++中应该避免使用NULL指针（因为它是一个宏，应该避免在C++中使用宏，见 `8.1.4 <chapter-8.rst#genconst>`_ ）。相比，基本可以总是使用0。

几乎是总是，但并不是一直都是。因为C++允许函数重载（ `2.5.4`_ 节）。开发者可能会面对意外的函数选择，如下面的情况下：

::

  #include <stdio.h>

  void show(int val)
  {
      printf("Integer: %d\n", val);
  }

  void show(double val)
  {
      printf("Double: %lf\n", val);
  }

  void show(char const *val)
  {
      printf("String: %s\n", val);
  }

  int main()
  {
      show(12);
      show(3.1415);
      show("Hello World!\n");
  }

这种情况下，开发者打算调用show(char const \*)，设计上可能调用了show(0)
。但是这不起作用，因为0被解析为int，所以show(int)被调用。调用show(NULL)同样也不起作用，因为C++定义NULL为0，而不是((void \*)0)。所以还是会调用show(int)。要解决此类问题，C++标准引入了nullptr关键字来代表0指针。在这个例子中，开发者应该调用show(nullptr)来避免选择错误的函数。nullptr同样可以用来初始化指针变量，如：

;;

  int \*ip = nullptr;      // OK
  int value = nullptr;    // error: value is no pointer

2.5.7：void参数列表
```````````````````
在C中，一个没有参数的函数原型，如

::

  void func();

意为改函数的的参数列表是不是原型：对于这样的函数原型，编译器不会检查调用时所传递的参数。在C中，void关键字用来明确说明函数没有参数，如

::

  void func(void);

因为C++强制进行语法检查，在C++中，空的参数列表表明没有参数，因此可以省略void关键字。

2.5.8：#define __cplusplus
``````````````````````````

每个遵循ANSI/ISO标准的编译器都会定义__cplusplus符号：就好像每个源文件都事先都通过#define __cplusplus制定定了这个符号。

接下来的几节，我们将会看到这个符号的使用例子。

2.5.9：使用标准C函数
````````````````````

普通C函数，在编译或者运行时库中的函数，都可以在C++程序中使用。但是，这些函数必须声明为C函数。

作为示例，下面的代码段声明了一个C的xmalloc函数：

::

  extern "C" void *xmalloc(int size);

这个声明和C中的声明类似，除了原型之前的extern "C"前缀。

一个声明C函数稍微不同的方式如下：

::

  extern "C"
  {
      // C-declarations go in here
  }

同样也可以在声明中添加预处理指令，例如，一个C头文件myheader.h的包含，其中声明的C函数可以被C++源文件包含：

::

  extern "C"
  {
      #include <myheader.h>
  }

尽管两种方式都可以使用，但是在C++源文件中很少会遇到。一个比较常用的声明外部C函数的方法将会在下节介绍。

2.5.10：对C和C++都适用的头文件
``````````````````````````````

预定义的_cplusplus符号可以定义外部的C函数结合起来提供了创建C和C++都适用的头文件的能力。这样的头文件可能会声明一组C和C++都可以使用的函数。

这样的头文件初始化如下：

::

    #ifdef __cplusplus
    extern "C"
    {
    #endif

        /* declaration of C-data and functions are inserted here. E.g., */
        void *xmalloc(int size);

    #ifdef __cplusplus
    }
    #endif

用这种方式初始化，一个普通的C头文件用extern "C" {}包含起来。{出现在最开始，}出现在文件的底部。#ifdef指令检测结合的类型：C或C++。标准的C头文件，例如stdio.h內建这种方式，因此可以被C和C++使用。

此外，C++头文件应该支持包含保护。在C++中，通常不希望在源文件中两次包含同一个头文件。要避免重复包含，可以在头文件中使用#ifndef指令。例如：

::

  #ifndef MYHEADER_H_
  #define MYHEADER_H_
      // declarations of the header file is inserted here,
      // using #ifdef __cplusplus etc. directives
  #endif

当预处理器第一次处理该文件时，还没有定义MYHEADER_H_符号还未定义。#ifndef条件生效，所有的声明都被处理，同时定义了MYHEADER_H_。

当再次处理该头文件时，MYHEADER_H_符号已经定义，因此在#ifndef和#endif指令之间的所有内容都被忽略。

在这种情况下，MYHEADER_H_只是起到一个标识的作用。可以使用头文件的大写的文件名作为该标识，然后把点换成下划线。

除了这些，通常c的头文件是.h扩展名，C++头文件没有扩展名。例如，包含iostream头文件后，可以使用标准流中的cin，cout和cerr，而不是包含iostream.h。本文当遵循这个约定，但也不是每处都这样。

关于头文件，还有更多要讨论的。 `7.11 <chapter-7.rst#classheader>`_ 会深入讨论C++最佳的头文件组织行时。

2.5.11：定义局部变量
````````````````````

尽管C语言中已经提供，但局部变量应该只有到使用的时候才定义（译注：原文中的说的C太老了，现在也可以做到）。相比于在复合语句前定义变量，虽然会有点不习惯，但是最终你会发现，它会增强代码的可读性，可维护性，并且更加有效率。我们建议在定义局部变量时，遵循下列规则：

* 局部变量应该在合适的位置创建，如前面的例子。不仅仅适用于for语句，同样适用于所有仅仅只是半路需要一个变量的地方。

* 一般而言，变量的作用于应该尽可能的小，更加的局部化。 When avoidable local variables are not defined at the beginning of functions but rather where they're first used.

* 避免使用全局变量认为是一个好的习惯。很容易搞乱一个变量是用来做什么的。 In C++ global variables are seldom required, and by localizing variables the well known phenomenon of using the same variable for multiple purposes, thereby invalidating each individual purpose of the variable, can easily be prevented.

If considered appropriate, nested blocks can be used to localize auxiliary variables. However, situations exist where local variables are considered appropriate inside nested statements. The just mentioned for statement is of course a case in point, but local variables can also be defined within the condition clauses of if-else statements, within selection clauses of switch statements and condition clauses of while statements. Variables thus defined are available to the full statement, including its nested statements. For example, consider the following switch statement: 

::

  #include <stdio.h>

  int main()
  {
      switch (int c = getchar())
      {
          case 'a':
          case 'e':
          case 'i':
          case 'o':
          case 'u':
              printf("Saw vowel %c\n", c);
          break;

          case EOF:
              printf("Saw EOF\n");
          break;

          case '0' ... '9':
              printf("Saw number character %c\n", c);
          break;

          default:
              printf("Saw other character, hex value 0x%2x\n", c);
      }
  }

注意变量c定义的位置：是在switch语句的表达式中定义的。这意味着c只对switch语句自身可见，包括内部的嵌套的语句（子语句），但是外面的区域是不可见的。

同样，也可以在if和while语句中使用这个方法：在if和while语句的条件表达式部分定义的变量，只对他们的嵌套的语句可见。不过，有一些额外说明：

* 定义的变量必须是初始化为数字或者逻辑值的变量
* 定义的变量不能嵌套在一个复杂的表达式之中（如使用了括号）

后面这条很好理解：if和while语句的表达式求值结果必须是一个逻辑值，值必须可以解释为0或非0的值。通常，这没什么问题，但是C++对象（像std::string）通常由函数返回。这样的对象可能也可能不会解释为数值类型的值。如果不是的化（还是拿std::string举例），那么这个变量就不能在条件语句和循环语句的条件表达式里定义。下面的例子是编译不通过的：

::

  if (std::string myString = getString())     // assume getString returns
  {                                           // a std::string value
      // process myString
  }

上面这个例子需要说明一下。我们经常会需要一个局部，然后立即在其初始化后对其测试。初始化和测试不能同时川县在一个表达式中。需要用两个嵌套语句。因此，下面的代码也不会编译：

::

  if ((int c = getchar()) && strchr("aeiou", c))
      printf("Saw a vowel\n");

如果遇到这种情况，或者使用两个嵌套语句，或者用一个嵌套复合语句局部化int c：

::

  if (int c = getchar())             // nested if-statements
      if (strchr("aeiou", c))
          printf("Saw a vowel\n");

  {                                  // nested compound statement
      int c = getchar();
      if (c && strchr("aeiou", c))
         printf("Saw a vowel\n");
  }

2.5.12：typedef关键字
`````````````````````

C++中仍可以使用typedef，但在定义union，struct或enum时就不必要使用了。例如下面的代码：

::

  struct SomeStruct
  {
      int     a;
      double  d;
      char    string[80];
  };

当要定义一个struct，union或其他复合类型的变量时，类型的标签可以用作类型的名字使用（上面例子中定义的SomeStruct）：

::

  SomeStruct what;

  what.d = 3.1415;

2.5.13：函数可以作为结构体的成员
````````````````````````````````

C++中，可以将函数定义为结构体成员。下面，我们就遇到了第一个具体的对象的例子：如之前讲述（ `2.4 <>`_ ），对象是一个包含数据的结构，有专门的函数用来操作这些数据。

点Point的struct可以用下面的代码定义。在这个结构中，声明了两个整形的数据成员和一个函数draw。

::

  struct Point            // definition of a screen-dot
  {
      int x;              // coordinates
      int y;              // x/y
      void draw();        // drawing function
  };

在绘图软件中，还可以定义一个类似的结构来代表一个像素。对于这个结构，应该注意：

* draw函数仅仅是申明。实际的函数定义应该在别的地方（结构体中的函数会在 `3.2 <chapter-2.rst#functionsinstructs>`_ 中进一步讨论）。

* Point结构的大小等于两个int的大小。在结构体内生灵函数不会影响到它的大小。编译器会实现只有在Point的上下文中才可使用draw函数。

Point结构可以如下使用：

::

  Point a;                // two points on
  Point b;                // the screen

  a.x = 0;                // define first dot
  a.y = 10;               // and draw it
  a.draw();

  b = a;                  // copy a to b
  b.y = 20;               // redefine y-coord
  b.draw();               // and draw it

如上例所示，结构体中的函数可以通过点（.）来选择（箭头（->）操作符使用在对象的指针上）。和选择数据域成员的方式一样。

使用这样的语法是处于不同的类型可以有相同的函数名称。例如一个代表园的结构体可能包含3个int值：两个代表坐标，一个代表半径。和Point结构类似，圆Circle结构体也可以有一个draw函数。

_`2.5.14：C++17标准引入的特性`
``````````````````````````````

C++17（也称为C++1z）标准会在下个Gnu g++的主板本中实现（7.0.0或之后）。

标准的工作草案是免费的，可以通过克隆这个git源获取： https://github.com/cplusplus/draft.git 。

C++注解会及时反馈出与C++17标准相关的改变。在本文当中有季节已经设计C++17标准。在表格目录或者索引中可以找到C++17的入口。

除了在各个章节中提到的，C++17标准还引入龄下面的特性：

**Evaluation order of operands of operators**

Up to C++17, the evaluation order of expressions of operands of binary operators is, except for the boolean operators and and or, not defined. C++17 changes this for postfix expressions, assignment expressions (including compound assignments), and shift operators: 

* Postfix expressions (like index operators and member selectors) are evaluated from left to right;
* Assignment expressions are evaluated from right to left;
* Operands of shift operators are evaluated from left to right. 

::

  first.second
  fourth += third = second += first
  first << second << third << fourth
  first >> second >> third >> fourth

In addition, when overloading an operator, the function implementing the overloaded operator is evaluated like the built-in operator it overloads, and not in the way function calls are generally ordered. 

**[[fallthrough]]**

 When statements that are nested under case entries in switch statements continue into subsequent case or default entries the compiler will issue a 'falling through' warning. If falling through is intentional the attribute [[fallthrough]] should be used. Here is an annotated example: 

::

  void function(int selector)
  {
      switch (selector)
      {
          case 1:
          case 2:             // no falling through, but merged entry points
              cout << "cases 1 and 2\n";
          [[fallthrough]];
          case 3:
              cout << "case 3\n";
  
          case 4:             // a warning is issued
              cout << "case 4\n";
          [[fallthrough]];    // error: nothing beyond
      }
  }

**[[maybe_unused]]**

This attribute can be applied to a class, typedef-name, variable, non-static data member, a function, an enumeration or an enumerator. When it is applied to an entity no warning is generated when the entity is not used. Example: 

::

  void fun([[maybe_unused]] size_t argument)
  {
      // argument isn't used, but no warning 
      // telling you so is issued
  }

**[[nodiscard]]**

The attribute [[nodiscard]] may be specified when declaring a function, class or enumeration. If a function is declared [[nodiscard]] or if a function returns an entity previously declared using [[nodiscard]] then the return value of such a function may only be ignored when explicitly cast to void. Otherwise, when the return value is not used a warning is issued. Example: 

::

  int [[nodiscard]] importantInt();
  struct [[nodiscard]] ImportantStruct { ... };
  
  ImportantStruct factory();
    
  int main()
  {
      importantInt();         // warning issued
      factory();              // warning issued
  }

