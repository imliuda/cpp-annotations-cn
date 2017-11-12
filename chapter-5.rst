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


