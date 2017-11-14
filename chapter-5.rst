第5章：string数据类型
=====================

C++提供了许多常规问题的解决方案。大多数这些工具都是STL库的一部分，或者实现为通用算法（ `chapter-19 <chapter-19.rst>`_ ）。

在这些C++开发者一次又一次开发的工具中处理文本的工具通常叫做字符串。C提供了基本的字符串操作。

为了处理文本，C++提供了std::string类型。在C++中，传统C中处理NTB字符串的库函数以废弃，取而代之的是采用string对象。C程序中许多问题都是由缓冲区移除，边界错误和分配问题引起的，追踪这些问题的起因都是由不恰当的使用传统C字符串函数导致的。使用C++字符串对象可以有效的避免这些问题。

实际上，string对象是类类型变量，在这个意义上，他们如同cin和cout流对象。在本节中，会讲解string类型对象的用法。主要是它们的定义和使用。字符串对象的成员函数通常是这样使用：

::

  stringVariable.operation(argumentList)

例如，如果string1和string2都是std::string类型的变量，那么

::

  string1.compare(string2)

用来比较两个字符串。

除了使用常规的成员函数方式，string类还提供了各种各样的操作符，像赋值（=）和比较（==）操作符。使用操作符会让代码更容易阅读和理解，对于比较功能，相对于使用成员函数，通常更倾向于使用操作符，而不是这样

::

  if (string1.compare(string2) == 0)

而是更加倾向于这种方式：

::

  if (string1 == string2)

要定义和使用string对象，必须要包含<string>头文件。如果只是申明字符串类型，可以包含iosfwd头文件。

除了std::string类型，string头文件还定义了下列string类型：

* std::wstring，由wchar_t字符构成的string类型；
* std::u16string，由char16_t字符构成的string类型；
* std::u32string，由char32_t字符构成的string类型；

5.1：字符串的操作
-----------------

一些字符串操作会返回索引。如果未能找到相应的索引，则返回string::npos。该值是string::size_type类型的符号值，是一个无符号整型值。

所有的字符串成员函数都可以使用string对象作为参数，也接受NTBS(null terminated byte string)类型参数。对于字接收字符串对象的操作符同样适用。

一些字符串成员使用迭代器。迭代器会在 `18.2 <chapter-18.rst#interators>`_ 正式介绍。使用迭代器的成员函数会在下一节（ `5.2 <stringoverview>`_ ）列出，但是本章不涉及迭代器的概念。

字符串支持相当多的成员函数和操作符。本节会对这些特性做一个概述，接下来的几节将会详细讨论。本质上：C++字符串是及其有用的，没有理由去使用C的字符串处理函数。C++字符串负责所有的必要的内存管理，因此使用C++字符串可以避免C中遇到的问题。Strings do come at a price, though. The class's extensive capabilities have also turned it into a beast. 要学习和掌握string的所有特性是有难度的，并且最终你会发现并没有你所期待的所有特性。例如，std::string没有提供忽略大小写的比较操作。最终，它并没有那么简单。在某种程度上，它是隐藏的，在C++注解中，要学写哪些东西还为之过早。替代的是，C的标准库中提供了有用的函数，我们可以借助这些函数，只要我们明白它们的限制，并且能够避免它们的陷阱。所以现在，要对两个std::string对象str和str2进行忽略大小写的比较，我们可以这样做：

::

  strcasecmp(str1.c_str(), str2.c_str());

string支持如下的功能：

* 初始化：

  当定义字符串对象的时候，它们总能正确的初始化。也就是说，它们总是处理合法的状态。字符串可以初始化为空，或者使用已经存在的字符串进行初始化。

* 赋值：

  可以给string对象赋新值。新值可以使用成员函数（如assign）进行赋值，也可以使用平常用的赋值操作符（如=）赋值。此外，还接受字符缓冲区赋值。

* 转换：

  string对象的部分或者全部内容可以转换为C的字符串。字符串的内容同样可以处理为一些列的原始二进制字节，不必要非以0值字符结尾。in many situations plain characters and C strings may be used where std::strings are accepted as well.

* 分解：

  可以使用熟悉的索引操作符（[]）获取一个string中的独立字符，允许我们访问或修改字符串中间的某个值。

* 比较：

  string可以和其他字符串比较（NTBS）。包含下面这些熟悉的luigi比较运算符==，！=，<，<=，>，>=。也有成员函数提供更细致的比较。

* 修改：

  可以通过多种方式修改字符串的内容。可以使用运算符向字符串添加信息，或者想字符串中间插入信息，或者替换和清空字符串的内容。

* 交换：

  string的交换功能允许我们原则上交换两个string对象的内容，而不需要一个按字节拷贝字符串内容。

* 搜索：

  可以在string对象中搜索字符，字符集或一些列字符，可以从任意位置开始，而且可以从正向或方向搜索。

* 流I/O：

  可以将字符串插入到流中，也可从流中提取字符串。除了普通字符串提取之外，还可以读取文本文件的行，不会有缓冲区溢出的风险。由于提取和插入操作是基于流的，所以I/O设备是独立于设备的。

5.2：std::string参考
--------------------

本节讲解字符串成员函数和相关运算符。下面的各个小节将会分别讲解字符串的初始化，迭代器和成员函数。本节中使用了下列的术语：

* object总是指string对象；
* 参数总是string const &或char const \*类型，除非另有说明。处理参数的操作不会修改参数的内容；
* opos指对一个string对象的偏移量；
* apos值对参数的偏移量；
* on代表一个字符串对象中字符的个数（从opos开始）；
* an代表参数中字符的个数（从apos开始）；

opos和apos都必须指向已经存在的偏移量，否则会产生异常（ `10 <chapter-10.rst>` _）。相比，an和on可以超出可用的字符个数，在这种情况下，只考虑可用的字符。

许多成员函数对on，an和apos定义了默认的值。默认的偏移量是0，默认的on和an是string::npos，该值可以解释为“达到字符串末尾所需要的字符个数”。

对于从字符串末尾执行反向操作的成员函数，opos的默认值是最后一个字符的索引值，在默认情况下等于opos+1，代表在opos处结束的子字符串的长度。

在下面给出的成员函数的概述中，可以假定所有这些参数都接受默认值，除非另有说明。当然，如果函数需要其他参数，而不是接受默认值的参数，则不能使用默认参数值。

Of course, the default argument values cannot be used if a function requires additional arguments beyond the ones otherwise accepting default values. 

Several member functions accept iterators. Section `18.2 <chapter-18.rst#interators>`_ covers the technical aspects of iterators, but these may be ignored at this point without loss of continuity. Like apos and opos, iterators must refer to existing positions and/or to an existing range of characters within the string object's contents.

All string-member functions computing indices return the predefined constant string::npos on failure.

The s literal suffix to indicate that a std::string constant is intended when a string literal (like "hello world") is used. It can be used after declaring using namespace std or, more specific, after declaring using namespace std::literals::string_literals.

When string literals are used when explicitly defining or using std::string objects the s-suffix is hardly ever required, but it may come in handy when using the auto keyword. E.g., auto str = "hello world"s defines std::string str, whereas it would have been a char const * if the literal suffix had been omitted. 

5.2.1：初始化
*************

在字符串被定义后，能保证它们都处于一个合法的状态。在定义字符串时，可以通过下列的集中方式：可用下面这些字符串构造函数：

* string object：

  定义一个空的字符串。使用这种方式定义字符串不需要参数。

* string object(string::size_type count, char ch)：

  使用count个ch字符初始化。

* string object(string const &argument)：

  使用给定的参数初始化。

* string object(std::string const &argument, string::size_type apos, string::size_type an)：

  用一个指定的字符串对象，在apos起始位置，最多使用an个字符，初始化一个新的字符串。

* string object(InputIterator begin, InputIterator end)：

  使用两个迭代器定义的字符区间内的字符初始化。

5.2.2：迭代器
*************

迭代器的细节可参考 `18.2 <chapter-18.rst#interators>`_ 。
