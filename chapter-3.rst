第3章：C++第一印象
==================

本章会进一步探索C++。用不同的例子说明在结构体中声明函数；介绍类的概念；详细介绍类型转换；介绍几个新的类型和几个相对于C扩展的重要符号。

3.1：和C的显著差异
------------------

在开始进行”真正的“面向对象编程之前，我们先介绍一些和C编程明显的区别：不仅仅是C和C++，还有在C中没有的或者和C使用方式不同的语法结构和关键字。

3.1.1：使用const关键字
``````````````````````

尽管const也是C语法中的一部分，但是在C++中会比C中更加常用和重要，并且严格。

const关键字是一个修饰符，表示变量或参数的值不可以被修改。下面的例子中想要改变ival变量的值，是不成功的：

::

   int main()
   {
       int const ival = 3;     // a constant int
                               // initialized to 3

       ival = 4;               // assignment produces
                               // an error message
   }

这个例子演示了ival可以在定义是初始化为一个给定的值；之后尝试想更改这个值是不允许的。

相比C，用const声明的变量可以用来指定数组的大小(译注：C也可以)，如下例：

::
  int const size = 20;
  char buf[size];             // 20 chars big

const关键字还可以用在声明指针时，比如指针类型的参数。这样声明：

::

  char const *buf;

buf是一个指向字符的指针。无论buf指向的是什么，它的内容都不可以通过buf来改变：字符被声明为了常量。buf指针自身是可以改变的。像*buf = 'a'这样的语句是不予寻的，但是++buf则是可以的。

像下面这样声明：

::

  char const *buf;

buf自身是一个常量指针，不允许改变。但是它所指向的内容是可以噶变的。

最后，像这样声明：

::

  char const *const buf;

也是可以的。这里，无论指针自身还是其指向的内容都是不可变的。

const关键字的放置位置的规则是：无论const左边的是什么，都是不可变的。

尽管简单，这条规则却经常使用。例如Bjarne Stroustrup的表述（在 http://www.research.att.com/~bs/bs_faq2.html#constplacement ）：

  我应该把const放在类型的前面还是后面？

  我放在前面，但这只是品味问题。“const T”和“T const”都可以并且是一样的。例如：

  ::

    const int a = 1;        // OK
    int const b = 2;        // also OK

  我猜，第一种方式可能会让较少的程序员感到困惑（更地道些）。

但是我们已经见过了一个把const放在前面而产生了不期望的结果的例子，接下来就会看到。另外，放在前面的方式于const函数冲突，会在 `17.7 <chapter-7.rst#constfunctions>`_ 遇到。const函数中，const关键字放在函数名的后面，而不是前面。

定义或声明（不管有没有const）都应该从变量或函数标识往类型标识走：

  Buf is a const pointer to const characters

在可能会产生困惑的地方，这条规则尤其有用。In examples of C++ code published in other places one often encounters the reverse: const preceding what should not be altered. That this may result in sloppy code is indicated by our second example above:

::

  char const *buf;

其中哪个是常量？草草一看，指针不能改变（因为const在指针前面）。事实上，字符的值才是常量，当我们尝试编译一下下面这个程序就清楚了：

::

  int main()
  {
      char const *buf = "hello";

      ++buf;                  // accepted by the compiler
      *buf = 'u';             // rejected by the compiler
  }

编译在*buf = 'u'这句失败了，而不是++buf语句。

Marshall Cline的 `C++ FAQ <http://www.parashift.com/c++-faq-lite/const-correctness.html>`_ 给了同样的规则（段落18.5），相似的问题：

  [18.5] “const Fred* p”和“Fred* const p”的区别是什么？

  你得从右向左读这个指针的声明。

但是，Marshall Cline的建议还可以再改进下。这里有个方法，能让你不费力的解析非常复杂的声明：

1. 以读取变量的名字开始。
2. 读的足够远（向右读），直到声明结束或者一个右括号。
3. 返回到开始的地方，然后往回读（向左读），直到定义开始或者一个匹配的左括号。
4. 如果遇到了一个左括号，在你之前停止的地方重复步骤2。

让我们用这个秘诀分析下下面的声明。箭头表示每步应该读多远，箭头的方向表示向哪边读：

::

  char const *(* const (*(*ip)())[])[]

                           ip          Start at the variable's name:
                                           'ip' is

                           ip)         Hitting a closing paren: revert
                           -->

                         (*ip)         Find the matching open paren:
                         <-                'a pointer to'

                         (*ip)())      The next unmatched closing par:
                              -->          'a function (not expecting
                                            arguments)'

                       (*(*ip)())      Find the matching open paren:
                       <-                  'returning a pointer to'

                       (*(*ip)())[])   The next closing par:
                                 -->       'an array of'

              (* const (*(*ip)())[])   Find the matching open paren:
              <--------                    'const pointers to'

              (* const (*(*ip)())[])[] Read until the end:
                                    ->     'an array of'

  char const *(* const (*(*ip)())[])[] Read backwards what's left:
  <-----------                             'pointers to const chars'

把每步的结果结合起来，我们得到，char const \*(\* const (\*(\*ip)())[])[]意思是：ip是一个指向函数（无参数）的指针，返回一个指针，指向一个数组，数组的元素是常量指针，指针指向常量字符。这就是ip代表的意思；用这个方法，你可以解析你曾经遇到过的任何声明。

3.1.2：命名空间
```````````````

C++引入了命名空间的概念：所有的符号都定义在一个大的上下文中，叫做命名空间。命名空间可以避免名称冲突，例如，一个程序员想要定义可以sin函数计算角度，又可以同时使用标准的sin函数计算弧度。

在第 `4 <chapter-4.rst>`_ 章中，会详细讲解命名空间。现在，你只需要知道大多数的编译器都需要显式的对标准命名空间std的声明。所以，除非另有声明，所有示例代码都在隐式的使用

::

  using namespace std;

声明。所以，如果你打算编译C++注解中的示例代码，确定源文件中使用了上面的using声明。

3.1.3：域解析操作符::
`````````````````````

C++引入了几个新的操作符，其中之一就是域操作符（::）。这个操作符可以用在全局变量和局部变量同名的情况下：

::

  #include <stdio.h>

  double counter = 50;                // global variable

  int main()
  {
      for (int counter = 1;           // this refers to the
           counter != 10;             // local variable
           ++counter)
      {
          printf("%d\n",
                  ::counter           // global variable
                  /                   // divided by
                  counter);           // local variable
      }
  }

上面的代码中，域解析操作符用来寻址全局变量，而不是使用局部的同名变量。C++中，域解析操作符使用广泛，但是很少会遇到局部变量覆盖全局变量的情况，它的主要用途会在第 `7 <chapter-7.rst>`_ 章中讲到。

3.1.4：cout，cin和cerr
``````````````````````

和C类似，C++定义了标准输入和标准输出流，供应用程序使用。这些流是：

* cout，类似于stdout
* cin，类似于stdin
* cerr，类似于stderr

语法上，这些流不是用在函数上：而是分别使用插入操作符<<和取出操作符>>将数据写入到流或者从流读取数据。如下面的示例：

::

  #include <iostream>

  using namespace std;

  int main()
  {
      int     ival;
      char    sval[30];

      cout << "Enter a number:\n";
      cin >> ival;
      cout << "And now a string:\n";
      cin >> sval;

      cout << "The number is: " << ival << "\n"
              "And the string is: " << sval << '\n';
  }

程序从cin流（通常是键盘）中读取一个数字和一个字符串，然后打印这些数据到cout中。关于流，请注意：

* 标准流是在iostream头文件中声明的。在C++注解的示例中，这个头文件经常没有明确的指出。当要使用这些流的时候，必须要包含进来。相对于使用using namespace std;语句，期望包含#include <iostream>来在示例中使用标准流。

* cin，cout和cerr是类类型的变量。这样的变量通常称为对象。类在C++中广泛使用，第 `7 <chapter-7.rst>`_ 章中详细讲解。

* cin使用取出操作符>>从一个流里取数据，然后拷贝这些数据到变量中（如上例的ival）。后面我们会讲解C++中的操作符如何执行不同的动作，以及C++给他们定义了什么行为。我们已经提到了函数重载，C++操作符可以有多个定义，叫做操作符重载。

* cin，cout和cerr的操作符可以操作不同类型的变量。上面的例子cout << ival是打印一个证书的值，而cout << "Enter a number"是打印一个字符串。因此，操作符的动作由变量的类型决定。

* 取出操作符（>>）通过在文本流中取数据，对变量执行类型安全赋值。通常，取值操作符会跳过要取的值前的所有空白字符。

* 特殊符号用在特殊情形下。通常一行通过“\n”或'\n'结束。但是插入endl符号，这行数据结束，然后会冲洗内部的的缓冲。因此，通常避免使用endl，而是使用'\n'以在一定成都上提到代码的效率。

cin，cout和cerr不适C++语法的一部分。流是sotream头文件中的一部分。这和printf不属于C的语法一样，并且最初是有哪些认为功能非常重要的人编写，并将它们放入到运行时库中。
