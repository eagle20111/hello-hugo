---
title: "C++11、C++14、C++17、C++20新特性总结"
slug: ""
date: 2022-12-14T11:00:08+08:00
summary: ""
author: ["Jian"]
cover:
    image: ""
    alt: ""
tags: []
katex: true
mermaid: false
draft: true
---

ref:</br>
[1]. https://blog.csdn.net/qq_41854911/article/details/119657617

## C++ 11是什么，C++ 11标准的由来

C++ 这门编程语言的历史可以追溯至 1979 年，当时的 Bjarne Stroustrup（C++ 之父，后续简称 Stroustrup）还在使用 Simula 语言进行开发工作。

Simula 语言被认为是第一个面向对象的编程语言。Stroustrup 也非常赞赏 Simula 语言的这种特性，但由于实例开发中 Simula 语言的执行效率太低，所以此后不久，Stroustrup 开始从事“带类的C”编程语言的开发工作。

> 注意在开发初期，并没有 C++ 这个称谓。所谓“带类的C”，顾名思义就是在 C 语言的基础上，为其加入面向对象的思想（扩增一些写好的类和对象）。初期的 C++ 除了具备 C 语言的所有功能外，还具有类、基本继承、内联函数、默认函数参数以及强类型检查等简单功能。

不仅如此，Stroustrup 还在 CPre（C语言编译器）的基础上，专门为“带类的C”开发了一个编译器，称为 Cfront，它可以将带有类的 C 代码自动转换为普通 C 语言程序。值得一提的是在 1993 年，Cfront 因难以支持 C++ 异常机制被弃用。

1983 年，“带类的C”正式被称为“C++”，其中“++”就取自 C 语言中的“++”运算符，这也从侧面表明了 Stroustrup 对于 C++ 这门编程语言的定位。 与此同时，C++还增添了很多功能，比如虚函数、函数重载、引用、const 关键字以及 // 注释符号等。

> 在随后的几年时间里，C++ 得到了快速地发展。比如说， C++ 不断地被更新，类中增加了受保护成员（protected）和私有成员（private），并允许使用多继承等；Stroustrup 出版了 《带注释的C++参考手册》一书，其一度被当做 C++ 开发的重要参考；Borland 发布了 Turbo C ++编译器，该编译器包含有大量的第三方 C++ 库，极大便利了 C ++ 的开发，等等。

直到 1998 年，C++ 标准委员会发布了第一版 C++ 标准，并将其命名为 C++98 标准。据不知名人士透露，《带注释的C++参考手册》这本书对 C++98 标准的制定产生了很大的影响。

经过作者的不断迭代，一本书往往会先后发布很多个版本，其中每个新版本都是对前一个版本的修正和更新。C++ 编程语言的发展也是如此。截止到目前（2020年），C++的发展历经了以下 3 个个标准：

  - 2011 年，新的 C++ 11 标准诞生，用于取代 C++ 98 标准。此标准还有一个别名，为“C++ 0x”；</br>
  - 2014 年，C++ 14 标准发布，该标准库对 C++ 11 标准库做了更优的修改和更新；</br>
  - 2017 年底，C++ 17 标准正式颁布。</br>

> 所谓标准，即明确 C++ 代码的编写规范，所有的 C++ 程序员都应遵守此标准。</br>

值得一提的是在 C++ 11 标准之前，C++ 标准委员会还在 2003 年对 C++ 98 标准做了一次修改（称为 C++ 03 标准），但由于其仅仅修复了一些 C++ 98 标准中存在的漏洞，并未修改核心语法，因此人们习惯将这次修订和 C++ 98 合称为 C++98/03 标准。

以上 3 个标准中，相比对前一个版本的修改和更新程度，C++ 11 标准无疑是颠覆性的，该标准在 C++ 98 的基础上修正了约 600 个 C++ 语言中存在的缺陷，同时添加了约 140 个新特性，这些更新使得 C++ 语言焕然一新。读者可以这样理解 C++ 11 标准，它在 C++ 98/03 标准的基础上孕育出了全新的 C++ 编程语言，造就了 C++ 新的开始。

那么，C++ 11 标准到底包含哪些新特性呢？别急，接下来会分篇给大家做详细地讲解。

## C++ auto类型推导完全攻略

在 C++11 之前的版本（C++98 和 C++ 03）中，定义变量或者声明变量之前都必须指明它的类型，比如 int、char 等；但是在一些比较灵活的语言中，比如 C#、JavaScript、PHP、Python 等，程序员在定义变量时可以不指明具体的类型，而是让编译器（或者解释器）自己去推导，这就让代码的编写更加方便。

C++11 为了顺应这种趋势也开始支持自动类型推导了！C++11 使用 auto 关键字来支持自动类型推导。

**auto 类型推导的语法和规则**

在之前的 C++ 版本中，auto 关键字用来指明变量的存储类型，它和 static 关键字是相对的。auto 表示变量是自动存储的，这也是编译器的默认规则，所以写不写都一样，一般我们也不写，这使得 auto 关键字的存在变得非常鸡肋。

C++11 赋予 auto 关键字新的含义，使用它来做自动类型推导。也就是说，使用了 auto 关键字以后，编译器会在编译期间自动推导出变量的类型，这样我们就不用手动指明变量的数据类型了。

auto 关键字基本的使用语法如下：

```c++
auto name = value;
```
name 是变量的名字，value 是变量的初始值。

> 注意：auto 仅仅是一个占位符，在编译器期间它会被真正的类型所替代。或者说，C++ 中的变量必须是有明确类型的，只是这个类型是由编译器自己推导出来的

auto 类型推导的简单例子：

```c++
auto n = 10;
auto f = 12.8;
auto p = &n;
auto url = “http://c.biancheng.net/cplus/”;
```

下面我们来解释一下：
  - 第 1 行中，10 是一个整数，默认是 int 类型，所以推导出变量 n 的类型是 int。
  - 第 2 行中，12.8 是一个小数，默认是 double 类型，所以推导出变量 f 的类型是 double。
  - 第 3 行中，&n 的结果是一个 int* 类型的指针，所以推导出变量 p 的类型是 int*。
  - 第 4 行中，由双引号""包围起来的字符串是 const char* 类型，所以推导出变量 url 的类型是 const char*，也即一个常量指针。

我们也可以连续定义多个变量：

```c++
int n = 20;
auto *p = &n, m = 99;
```

先看前面的第一个子表达式，&n 的类型是 int*，编译器会根据 auto *p 推导出 auto 为 int。后面的 m 变量自然也为 int 类型，所以把 99 赋值给它也是正确的。

这里我们要注意，推导的时候不能有二义性。在本例中，编译器根据第一个子表达式已经推导出 auto 为 int 类型，那么后面的 m 也只能是 int 类型，如果写作m=12.5就是错误的，因为 12.5 是double 类型，这和 int 是冲突的。

还有一个值得注意的地方是：使用 auto 类型推导的变量必须马上初始化，这个很容易理解，因为 auto 在 C++11 中只是“占位符”，并非如 int 一样的真正的类型声明。

**auto 的高级用法**

auto 除了可以独立使用，还可以和某些具体类型混合使用，这样 auto 表示的就是“半个”类型，而不是完整的类型。请看下面的代码：

```c++
int  x = 0;
auto *p1 = &x;   //p1 为 int *，auto 推导为 int
auto  p2 = &x;   //p2 为 int*，auto 推导为 int*
auto &r1  = x;   //r1 为 int&，auto 推导为 int
auto r2 = r1;    //r2 为  int，auto 推导为 int
```
下面我们来解释一下：

  - 第 2 行代码中，p1 为 int* 类型，也即 auto * 为 int *，所以 auto 被推导成了 int 类型。
  - 第 3 行代码中，auto 被推导为 int* 类型，前边的例子也已经演示过了。
  - 第 4 行代码中，r1 为 int & 类型，auto 被推导为 int 类型。
  - 第 5 行代码是需要重点说明的，r1 本来是 int& 类型，但是 auto 却被推导为 int 类型，这表明当=右边的表达式是一个引用类型时，auto 会把引用抛弃，直接推导出它的原始类型。

接下来，我们再来看一下 `auto` 和 `const` 的结合：

```c++
int  x = 0;
const auto n = x;  //n 为 const int ，auto 被推导为 int
auto f = n;      //f 为 const int，auto 被推导为 int（const 属性被抛弃）
const auto &r1 = x;  //r1 为 const int& 类型，auto 被推导为 int
auto &r2 = r1;  //r1 为 const int& 类型，auto 被推导为 const int 类型`在这里插入代码片`
```

下面我们来解释一下：
  - 第 2 行代码中，n 为 const int，auto 被推导为 int。
  - 第 3 行代码中，n 为 const int 类型，但是 auto 却被推导为 int 类型，这说明当=右边的表达式带有 const 属性时， auto 不会使用 const 属性，而是直接推导出 non-const 类型。
  - 第 4 行代码中，auto 被推导为 int 类型，这个很容易理解，不再赘述。
  - 第 5 行代码中，r1 是 const int & 类型，auto 也被推导为 const int 类型，这说明当 const 和引用结合时，auto 的推导将保留表达式的 const 类型。


最后我们来简单总结一下 auto 与 const 结合的用法：
  - 当类型不为引用时，auto 的推导结果将不保留表达式的 const 属性；
  - 当类型为引用时，auto 的推导结果将保留表达式的 const 属性。

**auto 的限制**

前面介绍推导规则的时候我们说过，使用 auto 的时候必须对变量进行初始化，这是 auto 的限制之一。那么，除此以外，auto 还有哪些其它的限制呢？

1. auto 不能在函数的参数中使用。

这个应该很容易理解，我们在定义函数的时候只是对参数进行了声明，指明了参数的类型，但并没有给它赋值，只有在实际调用函数的时候才会给参数赋值；而 auto 要求必须对变量进行初始化，所以这是矛盾的。

2. auto 不能作用于类的非静态成员变量（也就是没有 static 关键字修饰的成员变量）中。

3. auto 关键字不能定义数组，比如下面的例子就是错误的：
```c++
char url[] = “http://c.biancheng.net/”;
auto str[] = url; //arr 为数组，所以不能使用 auto
```
4. auto 不能作用于模板参数，请看下面的例子：
```c++
template <typename T>
class A{
    //TODO:
};
int  main(){
    A<int> C1;
    A<auto> C2 = C1;  //错误
    return 0;
}
```

**auto 的应用**

*使用 auto 定义迭代器*

auto 的一个典型应用场景是用来定义 stl 的迭代器。

我们在使用 stl 容器的时候，需要使用迭代器来遍历容器里面的元素；不同容器的迭代器有不同的类型，在定义迭代器时必须指明。而迭代器的类型有时候比较复杂，书写起来很麻烦，请看下面的例子：
```c++
#include <vector>
using namespace std;
int main(){
    vector< vector<int> > v;
    vector< vector<int> >::iterator i = v.begin();
    return 0;
}
```
可以看出来，定义迭代器 i 的时候，类型书写比较冗长，容易出错。然而有了 auto 类型推导，我们大可不必这样，只写一个 auto 即可。

修改上面的代码，使之变得更加简洁：
```c++s
#include <vector>
using namespace std;
int main(){
    vector< vector<int> > v;
    auto i = v.begin();  //使用 auto 代替具体的类型
    return 0;
}
```
auto 可以根据表达式 v.begin() 的类型（begin() 函数的返回值类型）来推导出变量 i 的类型。

*auto 用于泛型编程*

auto 的另一个应用就是当我们不知道变量是什么类型，或者不希望指明具体类型的时候，比如泛型编程中。我们接着看例子：

```c++
#include <iostream>
using namespace std;
class A{
public:
    static int get(void){
        return 100;
    }
};
class B{
public:
    static const char* get(void){
        return "http://c.biancheng.net/cplus/";
    }
};
template <typename T>
void func(void){
    auto val = T::get();
    cout << val << endl;
}
int main(void){
    func<A>();
    func<B>();
    return 0;
}
```
运行结果：
```
100
```

本例中的模板函数 func() 会调用所有类的静态函数 get()，并对它的返回值做统一处理，但是 get() 的返回值类型并不一样，而且不能自动转换。这种要求在以前的 C++ 版本中实现起来非常的麻烦，需要额外增加一个模板参数，并在调用时手动给该模板参数赋值，用以指明变量 val 的类型。

但是有了 auto 类型自动推导，编译器就根据 get() 的返回值自己推导出 val 变量的类型，就不用再增加一个模板参数了。

下面的代码演示了不使用 auto 的解决办法：

```c++
#include <iostream>
using namespace std;
class A{
public:
    static int get(void){
        return 100;
    }
};
class B{
public:
    static const char* get(void){
        return "http://c.biancheng.net/cplus/";
    }
};
template <typename T1, typename T2>  //额外增加一个模板参数 T2
void func(void){
    T2 val = T1::get();
    cout << val << endl;
}
int main(void){
    //调用时也要手动给模板参数赋值
    func<A, int>();
    func<B, const char*>();
    return 0;
}
```

## C++ decltype类型推导完全攻略

`decltype` 是 C++11 新增的一个关键字，它和 auto 的功能一样，都用来在编译时期进行自动类型推导。不了解 auto 用法的读者请转到(《C++ auto》)[http://c.biancheng.net/view/6984.html]。

decltype 是“declare type”的缩写，译为“声明类型”。

既然已经有了 auto 关键字，为什么还需要 decltype 关键字呢？因为 auto 并不适用于所有的自动类型推导场景，在某些特殊情况下 auto 用起来非常不方便，甚至压根无法使用，所以 decltype 关键字也被引入到 C++11 中。

auto 和 decltype 关键字都可以自动推导出变量的类型，但它们的用法是有区别的：
```c++
auto varname = value;
decltype(exp) varname = value;
```
其中，varname 表示变量名，value 表示赋给变量的值，exp 表示一个表达式。

<font color=red><u>auto 根据=右边的初始值 value 推导出变量的类型，而 decltype 根据 exp 表达式推导出变量的类型，跟=右边的 value 没有关系。</font></u>

另外，**auto 要求变量必须初始化，而 decltype 不要求**。这很容易理解，auto 是根据变量的初始值来推导出变量类型的，如果不初始化，变量的类型也就无法推导了。decltype 可以写成下面的形式：

```c++
decltype(exp) varname;
```

**exp 注意事项**

原则上讲，exp 就是一个普通的表达式，它可以是任意复杂的形式，但是我们必须要保证 exp 的结果是有类型的，不能是 void；例如，当 exp 调用一个返回值类型为 void 的函数时，exp 的结果也是 void 类型，此时就会导致编译错误。

C++ decltype 用法举例：
```c++
int a = 0;
decltype(a) b = 1;  //b 被推导成了 int
decltype(10.8) x = 5.5;  //x 被推导成了 double
decltype(x + 100) y;  //y 被推导成了 double
```
可以看到，decltype 能够根据变量、字面量、带有运算符的表达式推导出变量的类型。读者请留意第 4 行，y 没有被初始化。

**decltype 推导规则**

上面的例子让我们初步感受了一下 decltype 的用法，但你不要认为 decltype 就这么简单，它的玩法实际上可以非常复杂。当程序员使用 decltype(exp) 获取类型时，编译器将根据以下三条规则得出结果：

  - 如果 exp 是一个不被括号( )包围的表达式，或者是一个类成员访问表达式，或者是一个单独的变量，那么 decltype(exp) 的类型就和 exp 一致，这是最普遍最常见的情况。</br>
  - 如果 exp 是函数调用，那么 decltype(exp) 的类型就和函数返回值的类型一致。</br>
  - 如果 exp 是一个左值，或者被括号( )包围，那么 decltype(exp) 的类型就是 exp 的引用；假设 exp 的类型为 T，那么 decltype(exp) 的类型就是 T&。</br>

为了更好地理解 decltype 的推导规则，下面来看几个实际的例子。

【实例1】exp 是一个普通表达式：

```c++
#include <string>
using namespace std;
class Student{
public:
    static int total;
    string name;
    int age;
    float scores;
};
int Student::total = 0;
int  main(){
    int n = 0;
    const int &r = n;
    Student stu;
    decltype(n) a = n;  //n 为 int 类型，a 被推导为 int 类型
    decltype(r) b = n;     //r 为 const int& 类型, b 被推导为 const int& 类型
    decltype(Student::total) c = 0;  //total 为类 Student 的一个 int 类型的成员变量，c 被推导为 int 类型
    decltype(stu.name) url = "http://c.biancheng.net/cplus/";  //total 为类 Student 的一个 string 类型的成员变量， url 被推导为 string 类型
    return 0;
}

```
这段代码很简单，按照推导规则 1，对于一般的表达式，decltype 的推导结果就和这个表达式的类型一致。

【实例2】exp 为函数调用：

```c++

/函数声明
int& func_int_r(int, char);  //返回值为 int&
int&& func_int_rr(void);  //返回值为 int&&
int func_int(double);  //返回值为 int
const int& fun_cint_r(int, int, int);  //返回值为 const int&
const int&& func_cint_rr(void);  //返回值为 const int&&
//decltype类型推导
int n = 100;
decltype(func_int_r(100, 'A')) a = n;  //a 的类型为 int&
decltype(func_int_rr()) b = 0;  //b 的类型为 int&&
decltype(func_int(10.5)) c = 0;   //c 的类型为 int
decltype(fun_cint_r(1,2,3))  x = n;    //x 的类型为 const int &
decltype(func_cint_rr()) y = 0;  // y 的类型为 const int&&
```

需要注意的是，exp 中调用函数时需要带上括号和参数，但这仅仅是形式，并不会真的去执行函数代码。

【实例3】exp 是左值，或者被`( )`包围：
```c++
using namespace std;
class Base{
public:
    int x;
};
int main(){
    const Base obj;
    //带有括号的表达式
    decltype(obj.x) a = 0;  //obj.x 为类的成员访问表达式，符合推导规则一，a 的类型为 int
    decltype((obj.x)) b = a;  //obj.x 带有括号，符合推导规则三，b 的类型为 int&。
    //加法表达式
    int n = 0, m = 0;
    decltype(n + m) c = 0;  //n+m 得到一个右值，符合推导规则一，所以推导结果为 int
    decltype(n = n + m) d = c;  //n=n+m 得到一个左值，符号推导规则三，所以推导结果为 int&
    return 0;
}
```
这里我们需要重点说一下左值和右值：左值是指那些在表达式执行结束后依然存在的数据，也就是持久性的数据；右值是指那些在表达式执行结束后不再存在的数据，也就是临时性的数据。有一种很简单的方法来区分左值和右值，对表达式取地址，如果编译器不报错就为左值，否则为右值。

**decltype 的实际应用**

auto 的语法格式比 decltype 简单，所以在一般的类型推导中，使用 auto 比使用 decltype 更加方便，你可以转到《C++ auto》查看很多类似的例子，本节仅演示只能使用 decltype 的情形。

我们知道，auto 只能用于类的静态成员，不能用于类的非静态成员（普通成员），如果我们想推导非静态成员的类型，这个时候就必须使用 decltype 了。下面是一个模板的定义：
```c++

#include <vector>
using namespace std;
template <typename T>
class Base {
public:
    void func(T& container) {
        m_it = container.begin();
    }
private:
    typename T::iterator m_it;  //注意这里
};
int main()
{
    const vector<int> v;
    Base<const vector<int>> obj;
    obj.func(v);
    return 0;
}
```

单独看 Base 类中 m_it 成员的定义，很难看出会有什么错误，但在使用 Base 类的时候，如果传入一个 const 类型的容器，编译器马上就会弹出一大堆错误信息。原因就在于，`T::iterator`并不能包括所有的迭代器类型，当 T 是一个 const 容器时，应当使用 const_iterator。

要想解决这个问题，在之前的 C++98/03 版本下只能想办法把 const 类型的容器用模板特化单独处理，增加了不少工作量，看起来也非常晦涩。但是有了 C++11 的 decltype 关键字，就可以直接这样写：

```c++
template <typename T>
class Base {
public:
    void func(T& container) {
        m_it = container.begin();
    }
private:
    decltype(T().begin()) m_it;  //注意这里
};
```

看起来是不是很清爽？

注意，有些低版本的编译器不支持T().begin()这种写法，以上代码我在 VS2019 下测试通过，在 VS2015 下测试失败。

## 汇总auto和decltype的区别

通过(《C++ auto》)[http://c.biancheng.net/view/6984.html]和(《C++ decltype》)[http://c.biancheng.net/view/7151.html]两节的学习，相信大家已经掌握了 auto 和 decltype 的语法规则以及使用场景，这节我们将 auto 和 decltype 放在一起，综合对比一下它们的区别，并告诉大家该如何选择。

**语法格式的区别**

auto 和 decltype 都是 C++11 新增的关键字，都用于自动类型推导，但是它们的语法格式是有区别的，如下所示：

auto varname = value; //auto的语法格式
decltype(exp) varname [= value]; //decltype的语法格式

其中，varname 表示变量名，value 表示赋给变量的值，exp 表示一个表达式，方括号[ ]表示可有可无。

auto 和 decltype 都会自动推导出变量 varname 的类型：

  - auto 根据=右边的初始值 value 推导出变量的类型；
  - decltype 根据 exp 表达式推导出变量的类型，跟=右边的 value 没有关系。

另外，auto 要求变量必须初始化，也就是在定义变量的同时必须给它赋值；而 decltype 不要求，初始化与否都不影响变量的类型。这很容易理解，因为 auto 是根据变量的初始值来推导出变量类型的，如果不初始化，变量的类型也就无法推导了。

auto 将变量的类型和初始值绑定在一起，而 decltype 将变量的类型和初始值分开；虽然 auto 的书写更加简洁，但 decltype 的使用更加灵活。

请看下面的例子：

```c++
auto n1 = 10;
decltype(10) n2 = 99;
auto url1 = "http://c.biancheng.net/cplus/";
decltype(url1) url2 = "http://c.biancheng.net/java/";
auto f1 = 2.5;
decltype(n1*6.7) f2;
```
这些用法在前面的两节中已经进行了分析，此处就不再赘述了。

**对 cv 限定符的处理**

「cv 限定符」是 const 和 volatile 关键字的统称：
  - const 关键字用来表示数据是只读的，也就是不能被修改；
  - volatile 和 const 是相反的，它用来表示数据是可变的、易变的，目的是不让 CPU 将数据缓存到寄存器，而是从原始的内存中读取。

在推导变量类型时，auto 和 decltype 对 cv 限制符的处理是不一样的。decltype 会保留 cv 限定符，而 auto 有可能会去掉 cv 限定符。

以下是 auto 关键字对 cv 限定符的推导规则：

  - 如果表达式的类型不是指针或者引用，auto 会把 cv 限定符直接抛弃，推导成 non-const 或者 non-volatile 类型。
  - 如果表达式的类型是指针或者引用，auto 将保留 cv 限定符。

下面的例子演示了对 const 限定符的推导：
```c++
//非指针非引用类型
const int n1 = 0;
auto n2 = 10;
n2 = 99;  //赋值不报错
decltype(n1) n3 = 20;
n3 = 5;  //赋值报错
//指针类型
const int *p1 = &n1;
auto p2 = p1;
*p2 = 66;  //赋值报错
decltype(p1) p3 = p1;
*p3 = 19;  //赋值报错
```
在 C++ 中无法将一个变量的完整类型输出，我们通过对变量赋值来判断它是否被 const 修饰；如果被 const 修饰那么赋值失败，如果不被 const 修饰那么赋值成功。虽然这种方案不太直观，但也是能达到目的的。

n2 赋值成功，说明不带 const，也就是 const 被 auto 抛弃了，这验证了 auto 的第一条推导规则。p2 赋值失败，说明是带 const 的，也就是 const 没有被 auto 抛弃，这验证了 auto 的第二条推导规则。

n3 和 p3 都赋值失败，说明 decltype 不会去掉表达式的 const 属性。

**对引用的处理**

当表达式的类型为引用时，auto 和 decltype 的推导规则也不一样；decltype 会保留引用类型，而 auto 会抛弃引用类型，直接推导出它的原始类型。请看下面的例子：

```c++
#include <iostream>
using namespace std;
int main() {
    int n = 10;
    int &r1 = n;
    //auto推导
    auto r2 = r1;
    r2 = 20;
    cout << n << ", " << r1 << ", " << r2 << endl;
    //decltype推导
    decltype(r1) r3 = n;
    r3 = 99;
    cout << n << ", " << r1 << ", " << r3 << endl;
    return 0;
}
```

运行结果：
```c++
10, 10, 20
99, 99, 99
```
**总结**
从运行结果可以发现，给 r2 赋值并没有改变 n 的值，这说明 r2 没有指向 n，而是自立门户，单独拥有了一块内存，这就证明 r 不再是引用类型，它的引用类型被 auto 抛弃了。

给 r3 赋值，n 的值也跟着改变了，这说明 r3 仍然指向 n，它的引用类型被 decltype 保留了。

auto 虽然在书写格式上比 decltype 简单，但是它的推导规则复杂，有时候会改变表达式的原始类型；而 decltype 比较纯粹，它一般会坚持保留原始表达式的任何类型，让推导的结果更加原汁原味。

从代码是否健壮的角度考虑，我推荐使用 decltype，它没有那么多是非；但是 decltype 总是显得比较麻烦，尤其是当表达式比较复杂时，例如：
```c++
vector nums;
decltype(nums.begin()) it = nums.begin();
```
而如果使用 auto 就会清爽很多：
```c++
vector nums;
auto it = nums.begin();
```
在实际开发中人们仍然喜欢使用 auto 关键字（我也这么干），因为它用起来简单直观，更符合人们的审美。如果你的表达式类型不复杂，我还是推荐使用 auto 关键字，优雅的代码总是叫人赏心悦目，沉浸其中。

## C++返回值类型后置（跟踪返回值类型）

在泛型编程中，可能需要通过参数的运算来得到返回值的类型。考虑下面这个场景：

```c++
template <typename R, typename T, typename U>
R add(T t, U u)
{
    return t+u;
}
int a = 1; float b = 2.0;
auto c = add<decltype(a + b)>(a, b);
```

我们并不关心 a+b 的类型是什么，因此，只需要通过 decltype(a+b) 直接得到返回值类型即可。但是像上面这样使用十分不方便，因为外部其实并不知道参数之间应该如何运算，只有 add 函数才知道返回值应当如何推导。

那么，在 add 函数的定义上能不能直接通过 decltype 拿到返回值呢？

```c++
template <typename T, typename U>
decltype(t + u) add(T t, U u)  // error: t、u尚未定义
{
    return t + u;
}
```

当然，直接像上面这样写是编译不过的。因为 t、u 在参数列表中，而 C++ 的返回值是前置语法，在返回值定义的时候参数变量还不存在。

可行的写法如下：

```c++
template <typename T, typename U>
decltype(T() + U()) add(T t, U u)
{
    return t + u;
}
```

考虑到 T、U 可能是没有无参构造函数的类，正确的写法应该是这样：

```c++
template <typename T, typename U>
decltype((*(T*)0) + (*(U*)0)) add(T t, U u)
{
    return t + u;
}
```

虽然成功地使用 decltype 完成了返回值的推导，但写法过于晦涩，会大大增加 decltype 在返回值类型推导上的使用难度并降低代码的可读性。

因此，在 C++11 中增加了**返回类型后置（trailing-return-type，又称跟踪返回类型）**语法，将 decltype 和 auto 结合起来完成返回值类型的推导。

返回类型后置语法是通过 auto 和 decltype 结合起来使用的。上面的 add 函数，使用新的语法可以写成：

```c++
template <typename T, typename U>
auto add(T t, U u) -> decltype(t + u)
{
    return t + u;
}
```

为了进一步说明这个语法，再看另一个例子：

```c++
int& foo(int& i);
float foo(float& f);
template <typename T>
auto func(T& val) -> decltype(foo(val))
{
    return foo(val);
}
```
如果说前一个例子中的 add 使用 C++98/03 的返回值写法还勉强可以完成，那么这个例子对于 C++ 而言就是不可能完成的任务了。

在这个例子中，使用 decltype 结合返回值后置语法很容易推导出了 foo(val) 可能出现的返回值类型，并将其用到了 func 上。

返回值类型后置语法，是为了解决函数返回值类型依赖于参数而导致难以确定返回值类型的问题。有了这种语法以后，对返回值类型的推导就可以用清晰的方式（直接通过参数做运算）描述出来，而不需要像 C++98/03 那样使用晦涩难懂的写法。

## C++11对模板实例化中连续右尖括号>>的改进

在 C++98/03 的泛型编程中，模板实例化有一个很烦琐的地方，那就是连续两个右尖括号（>>）会被编译器解释成右移操作符，而不是模板参数表的结束。

【实例】C++98/03 中不支持连续两个右尖括号的示例。

```c++
template <typename T>
struct Foo
{
      typedef T type;
};
template <typename T>
class A
{
    // ...
};
int main(void)
{
    Foo<A<int>>::type xx;  //编译出错
    return 0;
}

```

使用 gcc 编译时，会得到如下错误提示：
```shell
error: ‘>>’ should be ‘>>’ within a nested template argument list Foo<A>::type xx;
```
意思就是，`Foo<A<int>>`这种写法是不被支持的，要写成这样`Foo<A<int> >`（注意两个右尖括号之间的空格）。

这种限制无疑是很没有必要的。在 C++ 的各种成对括号中，目前只有右尖括号连续写两个会出现这种二义性。static_cast、reinterpret_cast 等 C++ 标准转换运算符，都是使用<>来获得待转换类型（type-id）的。若这个 type-id 本身是一个模板，用起来会很不方便。

现在在 C++11 中，这种限制终于被取消了。在 C++11 标准中，要求编译器对模板的右尖括号做单独处理，使编译器能够正确判断出>>是一个右移操作符还是模板参数表的结束标记（delimiter，界定符）。

不过这种自动化的处理在某些时候会与老标准不兼容，比如下面这个例子：

```c++
template <int N>
struct Foo
{
    // ...
};
int main(void)
{
    Foo<100 >> 2> xx;
    return 0;
}
```

在 C++98/03 的编译器中编译是没问题的，但 C++11 的编译器会显示：

error: expected unqualif?ied-id before ‘>’ token Foo<100 >> 2> xx;

解决的方法是这样写：

Foo<(100 >> 2)> xx; // 注意括号

这种加括号的写法其实也是一个良好的编程习惯，使得在书写时倾向于写出无二义性的代码。

**扩展阅读**

各种 C++98/03 编译器除了支持标准（ISO/IEC 14882：2003 及其之前的标准）之外，还自行做了不少的拓展。这些拓展中的一部分，后来经过了 C++ 委员会的斟酌和完善，进入了 C++11。

所以有一部分 C++11 的新特征，在一些 C++98/03 的老编译器下也是可以支持的，只是由于没有标准化，无法保证各种平台/编译器下的兼容性。比如像 Microsoft Visual C++2005 这种不支持 C++11 的编译器，在对模板右尖括号的处理上和现在的 C++11 是一致的。

## C++11使用using定义别名（替代typedef）

大家都知道，在 C++ 中可以通过 typedef 重定义一个类型：
```c++
typedef unsigned int uint_t;
```
被重定义的类型并不是一个新的类型，仅仅只是原有的类型取了一个新的名字。因此，下面这样将不是合法的函数重载：
```c++
void func(unsigned int);
void func(uint_t); // error: redefinition
```
使用 `typedef` 重定义类型是很方便的，但它也有一些限制，比如，无法重定义一个模板。

想象下面这个场景：
```c++
typedef std::map<std::string, int> map_int_t;
// …
typedef std::map<std::string, std::string> map_str_t;
// …
```
我们需要的其实是一个固定以 std::string 为 key 的 map，它可以映射到 int 或另一个 std::string。然而这个简单的需求仅通过 typedef 却很难办到。

因此，在 C++98/03 中往往不得不这样写：
```c++
template <typename Val>
struct str_map
{
    typedef std::map<std::string, Val> type;
};
// ...
str_map<int>::type map1;
// ...
```

一个虽然简单但却略显烦琐的 str_map 外敷类是必要的。这明显让我们在复用某些泛型代码时非常难受。

现在，在 C++11 中终于出现了可以重定义一个模板的语法。请看下面的示例：

```c++
template <typename Val>
using str_map_t = std::map<std::string, Val>;
// ...
str_map_t<int> map1;
```

这里使用新的 using 别名语法定义了 std::map 的模板别名 str_map_t。比起前面使用外敷模板加 typedef 构建的 str_map，它完全就像是一个新的 map 类模板，因此，简洁了很多。

实际上，using 的别名语法覆盖了 typedef 的全部功能。先来看看对普通类型的重定义示例，将这两种语法对比一下：

```c++
// 重定义unsigned int
typedef unsigned int uint_t;
using uint_t = unsigned int;
// 重定义std::map
typedef std::map<std::string, int> map_int_t;
using map_int_t = std::map<std::string, int>;
```

可以看到，在重定义普通类型上，两种使用方法的效果是等价的，唯一不同的是定义语法。

typedef 的定义方法和变量的声明类似：像声明一个变量一样，声明一个重定义类型，之后在声明之前加上 typedef 即可。这种写法凸显了 C/C++ 中的语法一致性，但有时却会增加代码的阅读难度。比如重定义一个函数指针时：
```c++
typedef void (*func_t)(int, int);
```
与之相比，using 后面总是立即跟随新标识符（Identifier），之后使用类似赋值的语法，把现有的类型（type-id）赋给新类型：
```c++
using func_t = void (*)(int, int);
```
从上面的对比中可以发现，C++11 的 using 别名语法比 typedef 更加清晰。因为 typedef 的别名语法本质上类似一种解方程的思路。而 using 语法通过赋值来定义别名，和我们平时的思考方式一致。

下面再通过一个对比示例，看看新的 using 语法是如何定义模板别名的。

```c++
/* C++98/03 */
template <typename T>
struct func_t
{
    typedef void (*type)(T, T);
};
// 使用 func_t 模板
func_t<int>::type xx_1;
/* C++11 */
template <typename T>
using func_t = void (*)(T, T);
// 使用 func_t 模板
func_t<int> xx_2;
```

从示例中可以看出，通过 using 定义模板别名的语法，只是在普通类型别名语法的基础上增加 template 的参数列表。使用 using 可以轻松地创建一个新的模板别名，而不需要像 C++98/03 那样使用烦琐的外敷模板。

需要注意的是，using 语法和 typedef 一样，并不会创造新的类型。也就是说，上面示例中 C++11 的 using 写法只是 typedef 的等价物。虽然 using 重定义的 func_t 是一个模板，但 func_t 定义的 xx_2 并不是一个由类模板实例化后的类，而是 void(*)(int, int) 的别名。

因此，下面这样写：
```c++
void foo(void (*func_call)(int, int));
void foo(func_t func_call); // error: redefinition
```
同样是无法实现重载的，func_t 只是 void(*)(int, int) 类型的等价物。

细心的读者可以发现，using 重定义的 func_t 是一个模板，但它既不是类模板也不是函数模板（函数模板实例化后是一个函数），而是一种新的模板形式：模板别名（alias template）。

其实，通过 using 可以轻松定义任意类型的模板表达方式。比如下面这样：
```c++
template
using type_t = T;
// …
type_t i;
```

type_t 实例化后的类型和它的模板参数类型等价。这里，type_t 将等价于 int。

## C++11支持函数模板的默认模板参数

在 C++98/03 标准中，类模板可以有默认的模板参数，如下：

```c++
template <typename T, typename U = int, U N = 0>
struct Foo
{
    // ...
};
```

但是却不支持函数的默认模板参数：

```c++
template <typename T = int>  // error in C++98/03: default template arguments
void func()
{
    // ...
}
```

现在这一限制在 C++11 中被解除了。上面的 func 函数在 C++11 中可以直接使用，代码如下：
```c++
int main(void)
{
    func();   //T = int
    return 0;
}
```

此时模板参数 T 的类型就为默认值 int。从上面的例子中可以看出，当所有模板参数都有默认参数时，函数模板的调用如同一个普通函数。但对于类模板而言，哪怕所有参数都有默认参数，在使用时也必须在模板名后跟随<>来实例化。

除了上面提到的部分之外，函数模板的默认模板参数在使用规则上和其他的默认参数也有一些不同，它没有必须写在参数表最后的限制。甚至于，根据实际场景中函数模板被调用的情形，编译器还可以自行推导出部分模板参数的类型。

这意味着，当默认模板参数和编译器自行推导出模板参数类型的能力一起结合使用时，代码的书写将变得异常灵活。我们可以指定函数中的一部分模板参数采用默认参数，而另一部分使用自动推导，比如下面的例子：
```c++
template <typename R = int, typename U>
R func(U val)
{
    return val;
}
int main()
{
    func(97);               // R=int, U=int
    func<char>(97);         // R=char, U=int
    func<double, int>(97);  // R=double, U=int
    return 0;
}
```

C++11 标准中，我们可以像 func(97) 这样调用模板函数，因为编译器可以根据实参 97 自行推导出模板参数 U 的类型为 int，并且根据返回值 val=97 推导出 R 的类型也为 int；而 func(97) 手动指定了模板参数 R 的类型为 char（默认模板参数将无效），并通过实参 97 推导出了 U = int；最后 func<double,int>(97) 手动指定的 R 和 U 的类型值，因此无需编译器自行推导。

再次强调，当默认模板参数和自行推导的模板参数同时使用时，若无法推导出函数模板参数的类型，编译器会选择使用默认模板参数；如果模板参数即无法推导出来，又未设置其默认值，则编译器直接报错。例如：
```c++

template <typename T, typename U = double>
void func(T val1 = 0, U val2 = 0)
{
    //...
}
int main()
{
    func('c'); //T=char, U=double
    func();    //编译报错
    return 0;
}
```

其中，func(‘c’) 的这种调用方式，编译器通过实参 ‘c’ 可以推导出 T=char，但由于未传递第 2 个实参，因此模板参数 U 使用的是默认参数 double；但 func() 的调用方式是不行的，虽然 val1 设置有默认值，但编译器无法通过该默认值推导出模板参数 T 的类型。由此不难看出，编译器的自动推导能力并没有想象的那么强大。

总的来说，C++11 支持为函数模板中的参数设置默认值，在实际使用过程中，我们可以选择使用默认值，也可以尝试由编译器自行推导得到，还可以亲自指定各个模板参数的类型。

## C++11在函数模板和类模板中使用可变参数

所谓可变参数，指的是参数的个数和类型都可以是任意的。提到参数，大家会第一时间想到函数参数，除此之外 C++ 的模板（包括函数模板和类模板）也会用到参数。

对于函数参数而言，C++ 一直都支持为函数设置可变参数，最典型的代表就是 printf() 函数，它的语法格式为：

```c++
int printf ( const char * format, ... );
```

`...`就表示的是可变参数，即 printf() 函数可以接收任意个参数，且各个参数的类型可以不同，例如：

```c++
printf("%d", 10);printf("%d %c",10, 'A');printf("%d %c %f",10, 'A', 1.23);
```

我们通常将容纳多个参数的可变参数称为参数包。借助 format 字符串，printf() 函数可以轻松判断出参数包中的参数个数和类型。

下面的程序中，自定义了一个简单的可变参数函数：

```c++
#include <iostream>
#include <cstdarg>
//可变参数的函数
void vair_fun(int count, ...){
    va_list args;
    va_start(args, count);
    for (int i = 0; i < count; ++i) {
        int arg = va_arg(args, int);
        std::cout << arg << " ";
    }
    va_end(args);
}

int main(){
    //可变参数有 4 个，分别为 10、20、30、40
    vair_fun(4, 10, 20, 30,40);
    return 0;
}
```




[1]. https://blog.csdn.net/qq_41854911/article/details/119657617