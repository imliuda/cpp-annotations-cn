# 第9章 类和内存分配
和C语言中的内存分配函数（如malloc）对比，C++中的内存分配是由new运算符完成的。malloc和new的主要区别如下：

 - malloc自身不清楚被分配的内存将作何用。例如，当为int类型分配内存时，编程者必须提供一个正确的表达式，用于表示分配多少个sizeof(int)大小的空间。相比之下，new只需指定一个类型就可，编译器会隐式调用sizeof运算符。使用new是类型安全的。
 - ~~malloc分配的内存会被calloc舒适化为指定的值~~（译者注：malloc不会调用calloc初始化，malloc分配的内存是未被初始化的，calloc分配的内存会被自动初始化为0）。如果分配的内存是为一个对象使用，这是不必要的。因为new运算符会根据该对象的构造函数对该内存进行特定的初始化。
 - 采用C中的内存分配函数，我们必须要检查这些函数的返回值是否为NULL，采用new分配内存时则不需要这样的检查，我们可以通过配置`new_handler`来指定new失败时要执行的动作。

free和delete的比较：delete运算符能够保证在回收某个对象的内存时，这个对象的析构函数会被调用。

本章重点要讲的就是创建对象和销毁对象时，构造函数和析构函数的自动执行。在C程序的开发中，许多问题都是由不正常的内存分配和内存泄露造成的，如内存未被分配，内存未被释放，内存未被初始化，内存访问越界等。虽然C++不能自动的解决这些问题，但它给我们提供了有用的工具来防止这类问题的发生。

在C中，malloc经常用来处理字符串。在string.h其中有一些字符串处理函数是基于malloc的，如strdup。这些函数在C++中，我们应避免使用这些函数，取而代之，我们应该使用string类以及new和delete运算符。

Memory allocation procedures influence the way classes dynamically allocating their own memory should be designed. 因此，除了new和delete运算符，本章还会讨论这些内容。首先，先介绍new和delete运算符，然后会讨论：

 - 析构函数：在对象被销毁时被调用的成员函数。
 - 赋值运算符：允许我们将一个对象赋值给同类的另一个对象。
 - this指针：在一个对象的成员函数被调用时，明确的引用这个对象。
 - 拷贝构造：创建一个对象副本的构造函数。
 - 移动构造：从一个匿名的临时变量穿件对象的构造函数。

## 9.1 new和delete运算符
C++定义了两个用来分配和释放内存的运算符，分别为new和delete。

下面是一个简单的示例程序来演示如何使用他们。一个int类型的指针指向new申请的内存，之后该内存通过delete进行释放。

```cpp
int *ip = new int;
delete ip;
```

下面是new和delete运算符的一些特性：

 - new和delete是运算符，因此使用时不需要加括号
 - new返回指向一个内存区域，该指针的类型为被分配内存的对象类型（上例中，返回一个int类型的指针）
 - new后面跟着一个类型名称作为操作数，因此能为该类新的对象分配正确数量的内存
 - new是一个类型安全的操作符，它总是能够返回一个指向和操作数相同类型对象的指针，并且该指针的类型和这个对象的类型相同
 - new可能会失败，但是开发者不必担心这个问题，程序中不需要像使用malloc时那样必须检测内存是否分配成功，在[9.2.2](#9.2.2)中会深入讨论new的这个特性
 - delete返回void
 - new和delete必须成对使用，一个由new申请的内存，最终要有一个相对应的delete执行，以免内存泄露的情况发生
 - delete能够安全的处理0指针（什么也不做）
 - delete只能释放由new申请的内存，不应该用来释放使用malloc申请的内存
 - C++中，malloc和相近的内存分配函数（如calloc）是被弃用的，因此我们应避免使用它们

new运算符既可以为基本类型分配内存，又可以给对象分配内存。当为基本类型或者没有构造函数的struct类型分配内存时，不能保证这些内存被初始化为0，但是我们可以为其提供一个初始化表达式。

```cpp
int *v1 = new int;          // not guaranteed to be initialized to 0
int *v1 = new int();        // initialized to 0
int *v2 = new int(3);       // initialized to 3
int *v3 = new int(3 * *v2); // initialized to 9
```

当为类类型分配内存时，如果类名后面指定了要使用的构造函数，该对象就会用这个指定的构造函数进行初始化。例如，为string对象分配内存，我们可以使用下面这些语句。

```cpp
string *s1 = new string;            // uses the default constructor
string *s2 = new string();          // same
string *s3 = new string(4, ' ');    // initializes to 4 blanks.
```

new除了可以为单个实体和数组分配内存外，还可以用来申请”原始内存（raw memory）“：operator new(sizeInBytes)。分配原始内存，返回void ×类型。下面的代码使用new分配了一块无特定用途的内存。尽管原始内存可以包含多个字符，但它不应该被作为字符数组，因为new分配的原始类型内存返回的是void ×指针，返回值可以赋值给一个void ×类型的变量。要想赋值给一个char ×类型的变量，通常是将其进行转换，如下：

```cpp
char *chPtr = static_cast<char *>(operator new(numberOfBytes));
```

The use of raw memory is frequently encountered in combination with the placement new operator, discussed in section [9.1.5](#9.1.5)。
### 9.1.1 申请数组
new[]用来申请数组。必须要在中括号中指明要分配元素的数量，并且，中括号前面要指明元素的类型。例如：

```cpp
int *intarr = new int[20];          // allocates 20 ints
string *stringarr = new string[10]; // allocates 10 strings.
```

new运算符和new[]运算符是不同的，我们将在[9.1.2](#9.1.2)中讨论这个问题。

采用new[]分配的数组被称为动态数组，它们实在程序的运行过程中被创建的。它们的生命周期可能要长于它们被创建时所在的函数，动态分配的内存可以一直存在，只要程序一直运行。

当用new[]创建基本类型或者对象的数组时，new[]必须提供一个类型，并且，需要在中括号中提供一个用来计算元素数量的表达式。编译器会根据所提供的类型和表达式来计算索要分配内存块的大小。当使用new[]时，数组元素被存储在连续的内存当中，之后，就可以使用数组索引来访问这些元素了。intarr[0]代表第一个int指，紧随其后是intarr[1]，intarr[2]...直到intarr[19]。

对于非**类**类型（基本类型，如下面的POD类型，它没有构造函数），new[]返回的内存区域不能保证被初始化为0。但是，我们可以在new表达式后面加上括号来将分配的内存初始化为0。例如：

```cpp
struct POD
{
    int iVal;
    double dVal;
};
new POD[5]();       // returns a pointer to 5 0-initialized PODs
new double[9]();    // returns a pointer to 9 0-initialized doubles
```

如果POD结构体中的某些成员希望在结构体初始化时，能够拥有一个具体的值，或者该结构体中包含了其他的结构体，并且被包含的结构体的数据成员定义了一个默认值，那么在这个结构体的初始化过程中，被包含的结构体的数据成员的构造器具有高的优先级，然后是初始化为0值。如下例：

```cpp
struct Data
{
    int value = 100;
};
struct POD
{
    int iVal = 12;
    double dVal;
    Data data;
};

POD *pp = new POD[5]();
```

pp指向5个`POD`对象，每个对象由他们自己的`iVal`成员，并被初始化为12，`dVal`被初始化为0，`data.value`初始化为100。

当new[]用来为类对象数组分配内存时，如果类定义了默认的构造函数，这些函数将会自动被使用。`new string[20]`将会分配一个包含20个初始化了的字符串对象。我们也可以调用非默认构造函数，并且会将尝试用这种方式（将在[13.8](#)中记性讨论）。

new[]运算符中的中括号里的表达式代表要为该数组分配的元素的个数。C++允许分配大小为0的数组。`new int[0]`在C++是合法的。然而，这并没有什么实际用途，并且容易让人迷惑，因此，我们应该避免这样使用。没有用，是因为它并没有指向任何元素，令人迷惑是指返回的是一个无用的空指针，然而，通常我们创建一个指向数组的指针时，会将其初始化为空指针，以便用来进行条件测试，如`if(ptr)`，而这正好和0大小数组的返回值是相同的，所以区分不出这个指针到底是什么...

如果不使用new[]，我们同样可以创建局部数组，这类数组不是动态数组，并且他们的生命周期只在定义他们所在区块有效。

一旦被分配，所有的数组都是固定的大小。没有简单的方式来增大或减小数组的容量。C++没有“renew”关键字，[9.1.3](#9.1.3)将会演示如何增大数组。

### 9.1.2 删除数组
动态申请的数组需要使用delete[]运算符进行删除。后面应该跟着一个指向之前由new[]申请的内存区域的指针。

当delete[]运算符的操作数是一个指向一个对象数组的指针时，下面两个动作会被执行：

 - 首先，数组中的每个对象都会调用析构函数。析构函数执行各种类型的清理工作。
 - 然后，这个指针指向的内存区域被释放

下面是一个示例程序，演示了如何申请和释放一个包含10个字符串的数组：

```cpp
std::string *sp = new std::string[10];
delete[] sp;
```

当删除动态申请的基本类型（primitive）的数组时，没有特殊的动作将会执行。如`int *it = new int[10]`，执行`delete[] it`，仅仅就是简单的it所指向的内存释放。但是，因为指针也是基本类型，所以，删除一个动态申请的指针数组时，讲不会执行这些指针所指向对象的析构函数。所以，下面的代码将会造成内存泄露：

```cpp
string **sp = new string *[5];
for (size_t idx = 0; idx != 5; ++idx)
    sp[idx] = new string;
delete[] sp;            // MEMORY LEAK !
```

在本示例中，delete[]所执行的唯一动作就是释放了这5个指向字符串对象的指针所占用的内存区域。

在这种情况下，我们应该使用下面的方式来释放内存：

 - 对数组中的每个元素执行delete
 - 输出数组

示例：

```cpp
for (size_t idx = 0; idx != 5; ++idx)
    delete sp[idx];
delete[] sp;
```

One of the consequences is of course that by the time the memory is going to be returned not only the pointer must be available but also the number of elements it contains. This can easily be accomplished by storing pointer and number of elements in a simple class and then using an object of that class.：

delete[]运算符和delete运算符是不同的，规则是：用new[]申请，就要用delete[]释放。

### 9.1.3 数组扩容
一旦申请，所有的数组都是具有固定大小。没有简单的方法扩大和缩小数组的容量。C++没有“renew”运算符。要扩大一个数组，需要执行下面几个比本步骤：

 - 申请一个更大的内存区域
 - 拷贝原来数组的内容到新的数组中
 - 删除原来的数组
 - 让指针指向新申请的数组

静态数组和局部数据不能被重新改变大小（译者注：请读者思考为什么？）。只有动态数组才能重新改变大小。例如：

```cpp
#include <string>
using namespace std;

string *enlarge(string *old, size_t oldsize, size_t newsize)
{
    string *tmp = new string[newsize];  // allocate larger array

    for (size_t idx = 0; idx != oldsize; ++idx)
        tmp[idx] = old[idx];            // copy old to tmp

    delete[] old;                       // delete the old array
    return tmp;                         // return new array
}

int main()
{
    string *arr = new string[4];        // initially: array of 4 strings
    arr = enlarge(arr, 4, 6);           // enlarge arr to 6 elements.
}
```

上例中增大数组的流程是有缺陷的。

 - 新数组需要newsize个构造函数被调用
 - 新数组中的string对象已经被初始化过了，然而马上又调用了oldsize次赋值操作
 - 旧数组中的所有对象必须要销毁

根据实际的情况，存在几种不同的解决办法来提升效率。可以使用指针数组（(requiring only the pointers to be copied, no destruction, no superfluous initialization）或者原始内存结合[placement new operator](#)（an array of objects remains available, no destruction, no superfluous construction）。

### 9.1.4 管理原始内存
正如我们所见，new运算符为对象申请内存，紧接着使用该对象的一个构造函数初始化该对象。同样，delete运算符调用对象的析构函数，紧接着释放由new为该对象分配的内存。

下一节，我们将会遇到new的另一种使用方式，允许我们用原始内存初始化对象，这里，原始内存是指完全由一系列字节组成的一块内存区域，该内存块既可以为静态的，亦可以动态申请的。

原始内存可以通过`operator new(sizeInBytes)`或者`operator new[](sizeInBytes)`来申请。返回的内存区域不应该被看成任何类型的数组，它们仅仅是一个动态申请的内存块，并且没有初始化操作的执行。

上面两个表达式都返回void ×类型，所有需要（静态）转换成我们想用使用的类型。示例：

```cpp
// room for 5 ints:
int *ip = static_cast<int *>(operator new(5 * sizeof(int)));
// same as the previous example:
int *ip2 = static_cast<int *>(operator new[](5 * sizeof(int)));
// room for 5 strings:
string *sp = static_cast<string *>(operator new(5 * sizeof(string)));
```

采用这种方式，为某个类型分配特定数目个对象时，operator new并不知道要分配的内存的数据类型和大小。因此，operator new和malloc是类似的。

与operator new相对应的是operator delete。operator delete（或相应的operator delete[]）要求一个void ×类型的操作数（因此任意类型的指针都可以传递给它）。该指针被看做一个指向原始内存的指针，会被释放，并且不会执行额外的操作。因此operator delete和free是类似的。要释放上面ip和sp指针所指向的内存，应该这样使用：

```cpp
// delete raw memory allocated by operator new
operator delete(ip);
operator delete[](ip2);
operator delete(sp);
```

### 9.1.5 placement new运算符
operator new运算符的不寻常形式被成为placement new运算符。在使用placement new时，必须要包含<memory>头文件。

... 稍后添加

## 9.2 析构函数

与构造函数对应，类可以定义析构函数。析构函数和构造函数是相对应的，它会在对象的生命周期结束时调用。通常，写够函数会被自动调用，但也有例外情况。动态申请的对象的析构函数不会自动调用，除此之外，当程序被`exit`调用中断时，只有已经初始化了的全局对象的析构函数会被自动调用，这种情况下，在函数中定义的布局类型对象的析构函数也不会自动调用。这也是在C++程序中避免试着用`exit`的原因。

析构函数遵循下面的规则：
 - 析构函数的名字有波浪线（~）加上类的名称构成
 - 析构函数没有参数
 - 析构函数没有返回值

析构函数是在他们的类中声明的，例如：

```cpp
class Strings
{
    public:
    Strings();
    ~Strings();     // the destructor
};
```

按照惯例，构造函数在类定义中会被首先声明，后面是析构函数的声明，接下来是其他成员函数的声明。

析构函数的主要工作就是确保在某个对象的生命周期结束时，该对象所申请的内存能够正确的被释放。参考下面的类定义：

```cpp
class Strings
{
    std::string *d_string;
    size_t d_size;

    public:
        Strings();
        Strings(char const *const *cStrings, size_t n);
        ~Strings();

        std::string const &at(size_t idx) const;
        size_t size() const;
};
```

构造函数的主要任务中是初始化该对象的数据成员，加入，该类的构造函数定义如下：

```cpp
Strings::Strings()
:
    d_string(0),
    d_size(0)
{}

Strings::Strings(char const *const *cStrings, size_t size)
:
    d_string(new string[size]),
    d_size(size)
{
    for (size_t idx = 0; idx != size; ++idx)
        d_string[idx] = cStrings[idx];
}
```

一个`Strings`类型的对象申请了内存，所以需要一个析构函数。析构函数可能会被自动调用，也可能不会。析构函数仅仅在对象是“完全构造”对象情况下才会被调用。

C++把这类对象看成完全构造：至少有一个构造函数能够正常的结束。过去是指构造函数，但是C++支持构造委派，一个对象可能会有多个构造函数被执行。因此说是“至少一个”构造函数。完全构造对象还遵循下列规则：
 - 局部非静态对象在他们被定义的的区块结束时，会自动调用析构函数。在函数内，最外层定义的对象的析构函数会在函数返回前自动调用。
 - 静态或全局对象的析构函数会在程序结束时执行
 - 动态创建的对象的析构函数会在delete该对象时被调用
 - 动态创建的数组中，每个成员对象的析构函数会在delete[]该数组时被调用
 - 通过`placement new`初始化的对象，必须要明确的调用该对象的析构函数。

析构函数的作用就是确保仅由该对象维护的内存能够被正确的释放。上例中Strings的析构函数要做的是删除d_string所指向的内存，实现如下：

```cpp
Strings::~Strings()
{
    delete[] d_string;
}
```

The next example shows Strings at work. In process a Strings store is created, and its data are displayed. It returns a dynamically allocated Strings object to main. A Strings * receives the address of the allocated object and deletes the object again. Another Strings object is then created in a block of memory made available locally in main, and an explicit call to ~Strings is required to return the memory allocated by that object. In the example only once a Strings object is automatically destroyed: the local Strings object defined by process. The other two Strings objects require explicit actions to prevent memory leaks.

```cpp
#include "strings.h"
#include <iostream>

using namespace std;;

void display(Strings const &store)
{
    for (size_t idx = 0; idx != store.size(); ++idx)
        cout << store.at(idx) << '\n';
}

Strings *process(char *argv[], int argc)
{
    Strings store(argv, argc);
    display(store);
    return new Strings(argv, argc);
}

int main(int argc, char *argv[])
{
    Strings *sp = process(argv, argc);
    delete sp;

    char buffer[sizeof(Strings)];
    sp = new (buffer) Strings(argv, argc);
    sp->~Strings();
}
```

### 9.2.1 再谈对象指针

对象申请和释放内存，我们使用new和delete。之所以使用new和delete，而不是malloc和free，是因为new和delete对调用相应对象的构造函数和析构函数。

使用new运算符为对象申请内存会经历两个步骤。首先，为该对象分配合适的内存空间；然后，调用该对象的构造函数，对该对象进行初始化。同样，释放一个对象也需经历两个步骤。首先，对象的析构函数被调用，用来释放由对象管理的内存；然后释放该对象自身所使用的的内存。

动态申请对象数组，同样可以使用new和delete。
