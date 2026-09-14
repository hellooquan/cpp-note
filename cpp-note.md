# C++ 易错知识点

## 目录

- [1. 加入了string这种数据类型](#1-加入了string这种数据类型)
  - [1.1 高频函数速查](#11-高频函数速查)
  - [1.2 每个函数的最简写法](#12-每个函数的最简写法)
  - [1.3 九个坑](#13-九个坑)
- [2. const_cast,static_cast,dynamic_cast](#2-const_caststatic_castdynamic_cast)
  - [2.1 const_cast](#21-const_cast)
  - [2.2 static_cast](#22-static_cast)
  - [2.3 dynamic_cast](#23-dynamic_cast)
- [3. 函数重载](#3-函数重载)
  - [3.1 可以形成重载的情形](#31-可以形成重载的情形)
  - [3.2 不可以形成重载的情形](#32-不可以形成重载的情形)
  - [3.3 引用形参](#33-引用形参)
- [4. 左、右值传递参数，&和&&](#4-左右值传递参数和)
- [5. 引用&的本质](#5-引用的本质)
- [6. 枚举](#6-枚举)
- [7. 引用返回值解析](#7-引用返回值解析)
- [8. 异常](#8-异常)
  - [8.1 栈展开：异常穿过函数时发生了什么](#81-栈展开异常穿过函数时发生了什么)
  - [8.2 catch 的匹配规则](#82-catch-的匹配规则)
  - [8.3 别在析构函数里抛异常](#83-别在析构函数里抛异常)
- [9. 深、浅拷贝](#9-深浅拷贝)
- [10. 拷贝构造 T(const T &t)](#10-拷贝构造-tconst-t-t)
- [11. 移动构造 T(T &&t)](#11-移动构造-tt-t)
- [12. 匿名类对象](#12-匿名类对象)
- [13. std::move](#13-stdmove)
- [14. `Cat(const std::string &name) : _name(name) {}` 和 `Cat(std::string name) : _name(std::move(name)) {}`](#14-catconst-stdstring-name--_namename--和-catstdstring-name--_namestdmovename-)
- [15. 初始化列表](#15-初始化列表)

## 1. 加入了string这种数据类型

一句话：高频函数就三类——问大小、拿一段、改它自己。带"改"的函数（insert / erase / replace / += / clear …）一律原地生效，不留原样；find / substr / 比较这类只读，不动原串。

### 1.1 高频函数速查

| 函数 | 作用 |
| --- | --- |
| `size()` / `length()` | 元素个数（返回 `size_t`） |
| `empty()` | 是否为空 |
| `s[i]` / `s.at(i)` | 取字符：`[]` 不检查越界，`at` 越界抛异常 |
| `front()` / `back()` | 首字符 / 尾字符 |
| `s += "a"` / `append()` / `push_back('c')` | 加串 / 加串 / 加单个字符 |
| `insert(pos, s)` | 在下标 pos 处插入 |
| `erase(pos, n)` | 从 pos 起删 n 个 |
| `replace(pos, n, s)` | 从 pos 起 n 个字符换成 s |
| `pop_back()` / `clear()` | 删末尾一个 / 清空 |
| `find(s)` / `find(s, pos)` / `rfind(s)` | 首次出现 / 从 pos 往后找 / 最后一次出现 |
| `string::npos` | find 找不到时的返回值（不是 -1） |
| `substr(pos, n)` | 截取一段（起点 + 个数），不动原串 |
| `==` `<` `>` / `compare()` | 整串比较（别用 strcmp） |
| `c_str()` / `data()` | 取出 `const char*` 交给 C API |
| `stoi` / `stod` / `to_string` | 字符串 ↔ 数字 |
| `getline(cin, s)` | 读一整行（空格保留） |

三个 resize 类的补充：`s.reserve(n)` 提前要容量、`s.resize(n)` 改长度（变长补 `'\0'`）、`s.capacity()` 看当前容量——用得少，知道有就行。

### 1.2 每个函数的最简写法

#### 长度与判空
```cpp
string s = "hello";
cout << s.size() << " " << s.length() << " " << s.empty();  // 5 5 0

string t;
cout << t.empty() << " " << t.size();                        // 1 0
```

#### 取字符
```cpp
string s = "hello";
s[0] = 'H';                          // 可以写：改成 "Hello"
cout << s << " " << s.at(1);         // Hello e
cout << s.front() << " " << s.back(); // H o

cout << s.at(9);   // 抛 std::out_of_range
                   // 抛异常：basic_string::at: __n (which is 9) >= this->size() (which is 5)
                   // s[9] 不抛，直接是未定义行为
```

#### 加内容
```cpp
string s = "hi";
s += " there";        // 加串
s.append("!");        // 同上，一个意思
s.push_back('?');     // 只收单个字符
cout << s << " " << s.size();   // hi there!? 10
```

#### 原地修改
```cpp
string s = "hello world";
s.insert(0, ">> ");            cout << s;   // >> hello world
s.erase(0, 3);                 cout << s;   // hello world
s.replace(6, 5, "C++");        cout << s;   // hello C++
```
参数都是**起点 + 个数**，没有"给终点下标"的写法。

#### 删除
```cpp
string s = "hello";
s.pop_back();                          // 删末尾一个
cout << s << " " << s.size();          // hell 4

s.clear();                             // 清空
cout << "[" << s << "] " << s.size() << " " << s.empty();   // [] 0 1
```

#### 查找
```cpp
string s = "hello world";
cout << s.find("o") << " " << s.find("o", 5) << " " << s.rfind("o");  // 4 7 7
cout << s.find("xyz");                    // 18446744073709551615 (= string::npos)
cout << (s.find("xyz") == string::npos);  // 1
```
`find` 找不到返回 `string::npos`（一个极大的无符号数），判断必须写 `== string::npos`，不能写 `== -1` 或 `< 0`。

#### 截取
```cpp
string s = "hello world";
cout << s.substr(6) << "|" << s.substr(0, 5);  // world|hello
cout << s;                                     // hello world（原串没动）
cout << s + "!";                               // hello world!（拼接也不动原串）
```

#### 比较
```cpp
string a = "abc", b = "abd";
cout << (a == b) << " " << (a < b) << " " << (a > b);  // 0 1 0
cout << a.compare(b) << " " << a.compare(a);           // -1 0
```
整串比较直接 `==` / `<`，它比的是内容；`compare` 返回 `<0 / 0 / >0`，用法和 `strcmp` 一样但不用碰 `c_str()`。

#### 交给 C API 与数字互转
```cpp
string s = "hello";
printf("%s %zu\n", s.c_str(), strlen(s.c_str()));   // hello 5

cout << stoi("42") + 1;        // 43
cout << stod("3.5") * 2;       // 7
cout << to_string(7) + "!";    // 7!
cout << to_string(3.5);        // 3.500000
```

#### 读一整行
```cpp
string s;
cin >> s;                  // 遇空格/Tab/换行就停：输入 "hello world" 得到 [hello] 长度 5
cin.ignore(1000, '\n');    // 吃掉这一行剩下的内容
getline(cin, s);           // 读一整行，空格保留，换行丢掉：[foo bar baz] 长度 11
```

#### 遍历
```cpp
string s = "abc";
for (char c : s) cout << c << " ";                    // a b c
for (size_t i = 0; i < s.size(); i++) cout << s[i];   // abc
```
下标遍历时 `i` 用 `size_t`，因为 `s.size()` 就是它。

### 1.3 九个坑

1. **`size()` 返回无符号 `size_t`**。实测 `string("abc").size() - 5` 打印 18446744073709551614（写成 `(int)s.size() - 5` 才是 -2）。所以倒序遍历别写 `for (int i = s.size()-1; i >= 0; i--)`——条件永远为真，越界跑到崩溃；标准写法是 `for (size_t i = s.size(); i-- > 0; )`。
2. **`find` 失败返回 `string::npos`，不是 -1**。实测它打印出来是 18446744073709551615，判断写 `if (s.find(x) == string::npos)`。
3. **`s[i]` 不检查、`at()` 检查**：要"错了马上炸"用 `at`（抛异常，和异常处理的 catch 正好接上），要更快用 `[]`；`[]` 越界是未定义行为，不一定立刻报错。
4. **`erase` / `replace` / `substr` 的参数是"起点 + 个数"**，没有终点形式；`substr` 只读不动原串，`erase` / `replace` 原地改。
5. **`c_str()` 返回的指针是借来的**：字符串一改（可能重新分配）、对象一销毁，指针就作废。实测 `const char *p = s.c_str(); s += " world"; cout << p;` 这次恰好还打印出 "hello world"，看着正常——正因为不报错，这个雷更难发现；想留着就 `strcpy` 一份。
6. **`stoi` 不检查尾巴**：实测 `stoi("12abc")` = 12，不报错；一个数字都没有抛 `invalid_argument`，超出 int 范围抛 `out_of_range`。
7. **整串比较用 `==`**，它比内容，不要写 `strcmp(s.c_str(), t.c_str())`。
8. **`push_back` 只收单字符**，加串用 `+=` / `append` / `insert`。
9. **`getline` 前面若有 `cin >>`**：缓冲区里剩下的换行要先 `cin.ignore(1000, '\n')` 吃掉，否则 `getline` 直接读到空行。

## 2. const_cast,static_cast,dynamic_cast

- const_cast：专用于去除指针或引用的 const 属性
- static_cast：与旧式转换相近，但提供了更易于查找的语法，并能有效识别不兼容类型
- dynamic_cast：专用于类类型的上下代际间的转换

### 2.1 const_cast

- const_cast 旨在去除标识符的 cv 限定属性（即 const 与 volatile）
- const_cast 只能作用于指针或引用类型

```cpp
int main()
{
    int i = 6;                  // 普通整型变量
    const int &ri = i;          // const 型引用，不可修改

    // ×: 以下语句错误
    ri = 8;

    // √: 去除 const 特性后，可以赋值
    const_cast<int &>(ri) = 8;
}
```

#### 处理常目标指针

```cpp
int main()
{
    int i = 6;
    const int *p = &i;          // 常目标指针 p，不可修改目标

    // ×: 试图修改常指针的目标，错误！
    *p = 8;

    // √: 去除 const 特性后，可以修改其目标
    *(const_cast<int *>(p)) = 8;

    // ×: 试图扩大权限，错误！
    // int *k = p;              // error: invalid conversion from 'const int*' to 'int*'
    //（同一个作用域里 k 只能声明一次，所以这句注释掉，只留下面可编译的写法）

    // √: 去除 const 特性后，可赋值给普通指针 k
    int *k = const_cast<int *>(p);
}
```

注意，虽然 const_cast 可以将常目标指针的 const 属性剔除掉，但它不能剔除普通变量和常指针本身的 const 属性。例如：

```cpp
int main()
{
    int i = 6;                  // 普通整型变量
    int *const p = &i;          // 常指针

    int j = 8;

    // ×: p 是常指针，无法修改其指向
    p = &j;

    // ×: const_cast 不能去除常指针本身的 const 属性
    const_cast<int *>(p) = &j;
}
```

### 2.2 static_cast

static 意味着静态转换，静态的含义是操作的过程只发生在编译阶段，而不是运行阶段，静态转换不涉及类型推理。

#### 增加可读性

```cpp
int main()
{
    float f = 3.14;

    // 旧式类型转换
    int i = (int)f;
    i = int(f);

    // 新式静态转换，等价于旧式转换
    // 但是，新式静态强转更具可读性，更容易被查找
    i = static_cast<int>(f);
}
```

#### 提高安全性

对于旧式类型转换，奉行了 C 语言的一贯作风：基本不进行任何逻辑判定，将灵活性和责任都丢给开发者。这样做的后果是可能会在某些比较难以察觉的地方埋下隐患，使用 static_cast 可以避免某些隐患。

```cpp
int main()
{
    int i = 6;                  // 普通整型数据
    float *pf;                  // 普通浮点指针

    // 旧式转换不进行任何合理性检查
    // 下面的代码，可以将类型问题瞒天过海
    // 编译器让其畅行无阻，开发者肉眼也难以察觉
    pf = (float *)&i;           // float * 与 int * 不兼容，照样通过编译

    // 使用 static_cast 遇到非兼容性类型转换，会提出警告甚至错误
    pf = static_cast<float *>(&i);  // g++ 报错：invalid 'static_cast' from type 'int*' to type 'float*'
}
```

### 2.3 dynamic_cast

一句话:dynamic_cast 是"带真身检查的向下转型"——手里拿着一个**父类指针**,想知道它指向的到底是哪个子类时用它。

三个前提:

- 类必须是**多态**的(至少有一个虚函数,一般是虚析构),否则编译不过
- 只能转指针或引用,不能转对象、不能转基本类型
- 它查的是**运行期的真身**(靠 RTTI 信息)

```cpp
#include <iostream>
#include <typeinfo>
using namespace std;

class Base    { public: virtual ~Base() {} };          // 有虚函数 = 多态类型
class Derived : public Base { public: void hi() { cout << "Derived::hi\n"; } };

int main()
{
    Base    b;
    Derived d;
    Base *p1 = &d;   // 父类指针,真身是 Derived
    Base *p2 = &b;   // 父类指针,真身是 Base

    Derived *q1 = dynamic_cast<Derived *>(p1);   // 真身对得上 -> 成功
    cout << "p1 转型: " << (q1 ? "成功" : "失败") << "\n";

    Derived *q2 = dynamic_cast<Derived *>(p2);   // 真身不对 -> nullptr,不崩
    cout << "p2 转型: " << (q2 ? "成功" : "失败(nullptr)") << "\n";

    try {
        Derived &rr = dynamic_cast<Derived &>(*p2);   // 引用版:没有空引用,只能抛
        (void)rr;
    } catch (const bad_cast &e) {
        cout << "引用版失败 -> bad_cast: " << e.what() << "\n";
    }
}
```

实测输出:

```
p1 转型: 成功
p2 转型: 失败(nullptr)
引用版失败 -> bad_cast: std::bad_cast
```

要点:**指针版失败给 nullptr(可以 if 判断),引用版失败抛 std::bad_cast**。

和 static_cast 的区别(这才是它存在的理由):

```cpp
#include <iostream>
using namespace std;

class Base    { public: virtual ~Base() {} int x = 1; };
class Derived : public Base { public: int y = 2; };

int main()
{
    Base b;
    Base *p = &b;
    Derived *q = static_cast<Derived *>(p);   // 编译期硬转,不查真身,编译一点警告没有
    cout << "static_cast 转出来的指针非空: " << (q != nullptr) << "\n";
    cout << "但真身其实是 Base,拿它当 Derived 用就是读越界内存\n";
}
```

实测输出:

```
static_cast 转出来的指针非空: 1
但真身其实是 Base,拿它当 Derived 用就是读越界内存
```

dynamic_cast 多花的那点运行期时间,买的就是这个检查。

两个编译期/运行期表现(实测):

- 类没有虚函数 → 直接编译报错,原文:
  `error: cannot 'dynamic_cast' 'pp' (of type 'struct Plain*') to type 'struct Kid*' (source type is not polymorphic)`
- 两个毫无关系的类 → g++ **不报错**,编译通过,运行期给 nullptr:实测 `A *pa = &a; B *pb = dynamic_cast<B *>(pa);` 结果是空指针
- 向上转型(子类指针 → 父类指针)不需要它,直接赋值就行:`Base *p = &d;`

坑:

- 父类忘了写虚函数 → 编译不过(一般父类都写 `virtual ~Base() {}`)
- 别拿它做流程控制:关系在编译期就能确定的,用 static_cast 或直接赋值
- 编译加 `-fno-rtti` 时 dynamic_cast 直接不可用

## 3. 函数重载

### 3.1 可以形成重载的情形

- 参数个数不同
- 参数类型不同
- 类方法（即类内部的函数）的 const 属性可以构成重载
- 普通指针与常目标指针可以构成重载

```cpp
// 参数个数不同，可以形成重载
void f1(int a);
void f1(int a, int b);

// 参数类型不同，可以形成重载
void f2(int a);
void f2(float b);

class A
{
    // 类方法的 const 属性，可以形成重载
    void f3() const;
    void f3();
};

// 普通指针与常目标指针，可以形成重载
void f4(char *p);
void f4(const char *p);

// 这两个函数签名不同，可以共存
void func(int &a);
void func(const int &a);
```

### 3.2 不可以形成重载的情形

- 函数名、函数参数列表完全一致
- 函数的返回值类型差异
- 静态函数声明（static）
- const 型变量（包括常指针）

```cpp
// 函数名、参数列表完全一致，仅靠返回值类型的差异，
// 无法形成重载，这两个函数将会冲突
void f1(int a);
float f1(int a);

// static 不能形成重载，以下两个函数将会冲突
int f2(int a);
static int f2(int a);

// const 型变量（包括常指针），不能形成重载
void f3(int a);
void f3(const int a);

void f4(char *p);
void f4(char *const p);
```

### 3.3 引用形参

引用类型的参数是否可以形成重载，要根据实参的具体情况来定：

- 如果实参为常量，那么可以重载
- 如果实参为变量，那么不可以重载

```cpp
void f(int a);
void f(int &a);

int main()
{
    int m = 100;

    // ×: 会引起二义性，两个版本都能跟参数完全匹配
    f(m);

    // √: 可以顺利调用，因为普通引用类型无法指向常量
    f(6);
}
```

## 4. 左、右值传递参数，&和&&

- 左值（lvalue）：有名字、有地址、能持续存在的表达式。可以放在赋值号左边
- 右值（rvalue）：临时值、字面量、即将销毁的表达式。不能取地址，通常只能放在赋值号右边

```cpp
void func1(int &a);
void func2(const int &a);

int main()
{
    // 不可传递参数，100 为常量，为右值
    func1(100);
    // 可以传递参数
    func2(100);

    // 不可以传递参数，a+1 为表达式，为右值
    int a = 100;
    func1(a + 1);
    // 可以传递参数
    func2(a + 1);

    // 可以传递参数
    func1(a);
    func2(a);
}
```

```cpp
void func1(int *a);
void func2(const int *a);

int main()
{
    int a = 100;
    int *p1 = &a;
    const int *p2 = &a;

    func1(p1);  // ✅ 正确
    func1(p2);  // ❌ 错误
    func2(p1);  // ✅ 正确
    func2(p2);  // ✅ 正确
}
```

补上右值引用(`&&`)那一半:

三种引用参数的分工:

| 参数写法 | 能接什么 | 想表达的意思 |
| --- | --- | --- |
| `T &` | 只接左值 | 我要改你 |
| `const T &` | 左值、右值都接 | 我只读你,不想拷贝 |
| `T &&` | 只接右值 | 我要把你的资源搬走(移动构造/移动赋值) |

重载决议实测:

```cpp
#include <iostream>
#include <utility>
using namespace std;

void f(int &x)  { cout << "左值引用 int&  <- " << x << "\n"; }
void f(int &&x) { cout << "右值引用 int&& <- " << x << "\n"; }

int main()
{
    int a = 1;
    f(a);                 // 变量,有名字 -> 左值
    f(2);                 // 字面量 -> 右值
    f(a + 1);             // 表达式结果 -> 右值
    f(std::move(a));      // 强转成右值

    int &&rr = 3;         // 右值引用变量
    f(rr);                // 但 rr 自己是有名字的变量 -> 走左值版本
}
```

实测输出:

```
左值引用 int&  <- 1
右值引用 int&& <- 2
右值引用 int&& <- 2
右值引用 int&& <- 1
左值引用 int&  <- 3
```

最后一条是最大的坑:**`int &&rr = 3;` 里的 rr 有名字,有名字就是左值**,直接传下去走的是左值版本,想当右值传要写 `f(std::move(rr))`。

判断左值/右值的口诀:有名字、能取地址、活得比这一行久 → 左值;字面量、表达式结果、函数返回的临时对象 → 右值。

坑:

- `const T &` 是唯一能同时绑左值和右值的引用,所以"只读的参数"一律写 `const T &`
- 右值引用变量本身是左值(见上面最后一条)
- 你笔记里已有的那条:普通引用绑右值编译报错 `cannot bind non-const lvalue reference of type 'int&' to an rvalue of type 'int'`

## 5. 引用&的本质

一句话:引用不是对象,它只是**给已有对象起第二个名字**(别名)。编译器背地里用常量指针实现,但语言层面看不到那层指针。

```cpp
#include <iostream>
using namespace std;

void byValue(int v) { v = 999; }
void byRef(int &v)  { v = 999; }

int main()
{
    int b = 10;
    int &r = b;

    cout << "&b == &r ? " << (&b == &r) << "\n";                       // 1
    cout << "sizeof(int)=" << sizeof(int) << " sizeof(r)=" << sizeof(r) << "\n";
    r = 20;                                                            // 改 r = 改 b
    cout << "改 r 之后 b = " << b << "\n";

    byValue(b); cout << "传值后 b = " << b << "\n";                    // 改不动
    byRef(b);   cout << "传引用后 b = " << b << "\n";                  // 改得动

    int arr[3] = {1, 2, 3};
    int &e = arr[1];
    cout << "引用数组元素 &e == &arr[1] ? " << (&e == &arr[1]) << "\n";
}
```

实测输出:

```
&b == &r ? 1
sizeof(int)=4 sizeof(r)=4
改 r 之后 b = 20
传值后 b = 20
传引用后 b = 999
引用数组元素 &e == &arr[1] ? 1
```

要点:

- **必须初始化**(`int &r;` 编译不过),而且绑定后不能改绑:`r = 20` 是改 b 的值,不是让 r 换个目标
- `sizeof(引用)` 得到的是被引用对象的大小,`&引用` 得到的是被引用对象的地址 —— 因为语言层面引用没有自己的地址
- 没有"引用的引用",也没有"引用的数组"
- 引用当参数 = 传地址但没有指针语法,不拷贝原对象(实测:传值改不动原变量,传引用改得动)
- 常引用能绑临时对象,并把临时对象的生存期延长到引用的作用域结束(第 12 节那条规则)
- 底层就是个"每次使用都自动解引用"的常量指针,所以运行期没有额外开销

坑:

- 引用做成员或做返回值时,第一件事是问"被引用的对象活多久" —— 见第 7 节
- 引用初始化必须绑**同类型**对象(const 引用可以绑兼容类型,代价是会产生临时对象)

## 6. 枚举

对于具有确定元素个数的数组、容器或集合，都可以使用枚举循环来逐个遍历元素。

```cpp
#include <iostream>
#include <list>
using namespace std;

int main(int argc, char const *argv[])
{
    list<int> numbers;
    numbers.push_back(1);
    numbers.push_back(2);
    numbers.push_back(3);

    for (int i : numbers)
        cout << i << endl;

    return 0;
}
```

## 7. 引用返回值解析

一句话:返回**引用**=把原件交给你,返回**值**=给你一份拷贝。区别落在三点:能不能改原件、有没有拷贝开销、会不会悬垂。

正确用法(返回的东西活得比你函数长):

```cpp
#include <iostream>
using namespace std;

int g = 100;
int &retGlobal() { return g; }          // 全局变量:安全
int  retValue()  { int x = 7; return x; }  // 返回值:一份拷贝

struct Counter {
    int n = 0;
    Counter &add(int k) { n += k; return *this; }   // 返回 *this 的引用:支持链式调用
};

int main()
{
    int &r = retGlobal();
    r = 200;
    cout << "改引用后 g = " << g << "  (返回引用 = 把原件给你)\n";
    cout << "retValue() = " << retValue() << "  (返回值 = 给你一份拷贝)\n";

    Counter c;
    c.add(1).add(2).add(3);
    cout << "链式调用后 c.n = " << c.n << "\n";
}
```

实测输出:

```
改引用后 g = 200  (返回引用 = 把原件给你)
retValue() = 7  (返回值 = 给你一份拷贝)
链式调用后 c.n = 6
```

错误用法(返回局部变量的引用):

```cpp
#include <iostream>
using namespace std;

int &retLocal() { int x = 5; return x; }   // x 在函数返回那一刻就死了

int main()
{
    int &bad = retLocal();
    cout << "读到: " << bad << "\n";       // 读悬垂引用
}
```

实测:g++ 编译时就给警告(只是警告,编译能过)

```
warning: reference to local variable 'x' returned [-Wreturn-local-addr]
```

运行结果:`Segmentation fault`,退出码 139 —— 读的是已经还给系统的栈内存。它有时候不炸,只是读到"看着正常的垃圾值",所以别用"这次没崩"判断对不对,靠编译器警告抓。

什么时候**必须**返回引用:运算符重载(`operator=`、`operator<<`、`operator+=`)基本都是返回 `*this` 的引用,标准库就是这么写的,不返回引用就没法 `a = b = c`、没法 `cout << a << b`。

什么时候**别**返回引用:局部变量、函数里 new 出来的临时对象(拿不到引用就没人 delete 了)。只读的大对象可以返回 `const T &` 省一次拷贝,前提还是它活得够久。

## 8. 异常

一句话:throw 抛出,catch 接住,中间经过的那一串函数会被"层层清场"再跳到能接住它的那个 catch。

### 8.1 栈展开：异常穿过函数时发生了什么

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

struct Guard
{
    string name;
    Guard(string n) : name(n) { cout << "构造 " << name << "\n"; }
    ~Guard()                  { cout << "析构 " << name << "\n"; }
};

void inner()  { Guard g("inner");  throw runtime_error("inner 抛的"); }
void middle() { Guard g("middle"); inner(); cout << "这行永远不会执行\n"; }

int main()
{
    try { Guard g("main"); middle(); }
    catch (const exception &e) { cout << "捕获到: " << e.what() << "\n"; }
    cout << "catch 之后继续往下跑\n";
}
```

实测输出:

```
构造 main
构造 middle
构造 inner
析构 inner
析构 middle
析构 main
捕获到: inner 抛的
catch 之后继续往下跑
```

要点:throw 之后,每一层的局部对象都按**构造的逆序析构**(这就叫栈展开 / stack unwinding),构造函数已经跑完的对象一个都不会漏;被跳过的代码永远不执行。C++ 里"用对象管资源"(RAII)之所以成立,就是因为异常也会走析构。

### 8.2 catch 的匹配规则

- catch 按**类型匹配**;派生类异常可以用基类 catch 接
- catch 顺序**从具体到笼统**:派生类写前面,基类写后面。把 `catch (const exception &)` 放前面,后面的具体 catch 就永远是死代码(实测:先写基类,`out_of_range` 就被它接走了)
- `catch (...)` 兜底接所有类型,但拿不到异常里的信息
- 常用标准异常:`std::exception`(基类,`what()` 给消息)、`runtime_error`(运行期逻辑错)、`out_of_range`(越界)、`invalid_argument`(参数不合法)、`bad_alloc`(new 失败)。第 1.3 节第 6 条 `stoi` 抛的那两个就是这套
- 自定义异常最省事的写法是继承标准异常:

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

struct MyError : runtime_error
{
    int code;
    MyError(int c) : runtime_error("我的错误"), code(c) {}   // 先把消息交给基类
};

int main()
{
    try { throw MyError(42); }
    catch (const MyError &e)   { cout << "精确捕获: " << e.what() << " code=" << e.code << "\n"; }
    catch (const exception &e) { cout << "兜底捕获: " << e.what() << "\n"; }

    try { throw MyError(7); }
    catch (const exception &e) { cout << "派生类异常也能用基类接: " << e.what() << "\n"; }

    try { throw out_of_range("越界了"); }
    catch (const exception &) { cout << "基类写在前面,具体的 catch 就轮不上了\n"; }
}
```

实测输出:

```
精确捕获: 我的错误 code=42
派生类异常也能用基类接: 我的错误
基类写在前面,具体的 catch 就轮不上了
```

### 8.3 别在析构函数里抛异常

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

struct Bad { ~Bad() { throw runtime_error("析构里抛的"); } };

int main()
{
    try { Bad b; throw runtime_error("先抛一个"); }
    catch (const exception &e) { cout << "正常情况这里会捕获: " << e.what() << "\n"; }
}
```

实测:编译就警告

```
warning: 'throw' will always call 'terminate' [-Wterminate]
note: in C++11 destructors default to 'noexcept'
```

运行结果:`terminate called after throwing an instance of 'std::runtime_error'` + Aborted,退出码 134。

原因:析构常常是在"已经有异常正在往外传"的时候被调用的,这时候再抛就没人能接,标准干脆规定直接 terminate。所以析构里出错只能记日志、吞掉,不能抛。

坑:

- throw 的类型和 catch 的类型对不上 → 没人接 → std::terminate(09-12 那次 `throw "除数不可为零"` 就是这类)
- 异常是给"罕见、就地没法处理"的情况用的,别拿它代替 if(有开销,也难读)
- 写了 catch 就要处理或继续往上抛,空 catch 是最难查的 bug

## 9. 深、浅拷贝

一句话:浅拷贝 = 成员**逐个照抄**(指针成员照抄的是地址,于是两个对象共用一块内存);深拷贝 = 指针成员**重新申请一块内存,把内容搬一份**过去。

默认的拷贝构造 / 拷贝赋值做的都是浅拷贝。带指针成员的类最容易中招:

```cpp
#include <iostream>
#include <cstring>
using namespace std;

struct Shallow
{
    char *p;
    Shallow(const char *s) { p = new char[strlen(s) + 1]; strcpy(p, s); }
    ~Shallow() { delete[] p; }
    // 没写拷贝构造 -> 编译器给的默认版是浅拷贝
};

int main()
{
    Shallow a("hello");
    Shallow b = a;                 // 浅拷贝
    cout << "a.p == b.p ? " << (a.p == b.p) << "\n";
    b.p[0] = 'H';                  // 改 b 也改了 a
    cout << "a.p = " << a.p << "\n";
    cout << "退出 main 开始析构 → 同一块内存被 delete 两次\n";
}
```

实测:

```
free(): double free detected in tcache 2
Aborted     (退出码 134)
```

注意连它自己的 cout 都没打印出来 —— 崩溃时 stdout 缓冲里的内容会丢,看不到输出不等于没跑到那行。成因就是两个对象析构时对同一块内存 `delete[]` 了两次。

深拷贝版(自己写拷贝构造 + 拷贝赋值):

```cpp
#include <iostream>
#include <cstring>
using namespace std;

struct Deep
{
    char *p;
    Deep(const char *s) { p = new char[strlen(s) + 1]; strcpy(p, s); }
    Deep(const Deep &o) { p = new char[strlen(o.p) + 1]; strcpy(p, o.p); cout << "深拷贝构造\n"; }
    Deep &operator=(const Deep &o)
    {
        if (this != &o) { delete[] p; p = new char[strlen(o.p) + 1]; strcpy(p, o.p); }
        return *this;
    }
    ~Deep() { delete[] p; }
};

int main()
{
    Deep a("hello");
    Deep b = a;
    cout << "a.p == b.p ? " << (a.p == b.p) << "\n";
    b.p[0] = 'H';
    cout << "a.p = " << a.p << "  b.p = " << b.p << "\n";
    cout << "退出 main,两块内存各 delete 一次,正常\n";
}
```

实测输出:

```
深拷贝构造
a.p == b.p ? 0
a.p = hello  b.p = Hello
退出 main,两块内存各 delete 一次,正常
```

拷贝赋值的三条纪律:

1. 先判自赋值 `if (this != &o)` —— 不判的话 `a = a` 会先删掉自己的内存,再去读它
2. 先释放自己的旧资源,再申请新的(或者用"拷贝再交换"的写法)
3. 一定 `return *this`,否则 `a = b = c` 这种连写就断了

法则:**需要自己写析构的类,通常也需要自己写拷贝构造和拷贝赋值**(三法则);C++11 再加移动构造、移动赋值,合称五法则。典型需要它们的:类里攥着裸指针 / new 出来的资源。反过来,成员都是 `string` / `vector` 这类自己管资源的类型时,默认的拷贝就够用 —— 它们内部已经帮你深拷贝好了。

## 10. 拷贝构造 T(const T &t)

一句话:拷贝构造是"用同类型的另一个对象来初始化**新对象**"时调用的构造函数 —— 它是构造函数,不是赋值。

什么时候调它(实测,数字是程序自己打印的):

```cpp
#include <iostream>
using namespace std;

struct Track
{
    int id;
    Track(int i = 0) : id(i)              { cout << "  构造 " << id << "\n"; }
    Track(const Track &o) : id(o.id)      { cout << "  拷贝构造 " << id << "\n"; }
    Track(Track &&o) noexcept : id(o.id)  { o.id = -1; cout << "  移动构造 " << id << "\n"; }
    Track &operator=(const Track &o)      { id = o.id; cout << "  拷贝赋值 " << id << "\n"; return *this; }
    Track &operator=(Track &&o) noexcept  { id = o.id; o.id = -1; cout << "  移动赋值 " << id << "\n"; return *this; }
};

Track makeTrack() { Track t(9); return t; }
void byValue(Track t) { (void)t; }

int main()
{
    cout << "Track a(1);\n";            Track a(1);
    cout << "Track b = a;\n";           Track b = a;
    cout << "Track c(a);\n";            Track c(a);
    cout << "c = a;\n";                 c = a;
    cout << "Track d = makeTrack();\n"; Track d = makeTrack();
    cout << "byValue(a);\n";            byValue(a);
    cout << "Track e = 5;\n";           Track e = 5;
    cout << "e = std::move(d);\n";      e = std::move(d);
}
```

实测输出:

```
Track a(1);
  构造 1
Track b = a;
  拷贝构造 1
Track c(a);
  拷贝构造 1
c = a;
  拷贝赋值 1
Track d = makeTrack();
  构造 9
byValue(a);
  拷贝构造 1
Track e = 5;
  构造 5
e = std::move(d);
  移动赋值 9
```

一行行对着看:

- `Track a(1);` → 构造(小括号初始化也走构造函数)
- `Track b = a;` → **拷贝构造**,不是赋值:这里的等号是"初始化",b 那一刻还不存在
- `Track c(a);` → 也是拷贝构造,只是写法不同
- `c = a;` → 这次才是**拷贝赋值**(c 早就存在了)
- `Track d = makeTrack();` → 只打印一次"构造 9",**0 次拷贝 0 次移动**:返回临时对象时编译器直接在调用处就地构造(返回值优化,和你在第 13 节第 2 条看到的 NRVO 是一回事)
- `byValue(a);` → 拷贝构造(传值传参得复制一份)
- `Track e = 5;` → 构造,单参构造的隐式转换
- `e = std::move(d);` → 移动赋值

怎么记:`T b = a;` 长得像赋值,其实是**拷贝构造**(b 这时还不存在);`b = a;` 才是拷贝赋值(对象早就存在了)。

为什么参数必须写 `const T &`:

- 写成传值 `T(T t)` 编译直接报错:`error: invalid constructor; you probably meant 'T (const T&)'` —— 因为造形参 t 又得调拷贝构造,无限递归,标准干脆禁止
- 加 const 才能接常量对象和临时对象(临时对象是右值,普通引用绑不上)

默认版拷贝构造做什么:逐成员拷贝(浅拷贝,见第 9 节)。成员里有 `string` / `vector` 时,会调用它们自己的拷贝构造,所以默认版通常够用。

不想让对象被拷贝:`T(const T &) = delete;`(用到时报 use of deleted function 编译错误,语句本身写得很清楚)。

## 11. 移动构造 T(T &&t)

一句话:移动构造的参数是**右值引用**,它不复制资源,而是把源对象的资源"搬"过来(指针窃取),再把源对象置空。

什么时候调它:源是右值(临时对象、`std::move(x)`、函数返回的临时对象)。左值只会走拷贝构造 —— 这就是 `std::move` 存在的意义(第 13 节)。

和第 9 节的深拷贝构造对着看,区别只有两点:**参数类型是 `T &&`**、**搬完把源置空**:

```cpp
#include <iostream>
#include <cstring>
using namespace std;

struct Deep
{
    char *p;
    Deep(const char *s) { p = new char[strlen(s) + 1]; strcpy(p, s); }
    Deep(const Deep &o) { p = new char[strlen(o.p) + 1]; strcpy(p, o.p); }        // 拷贝:另申请一块
    Deep(Deep &&o) noexcept : p(o.p) { o.p = nullptr; }                           // 移动:拿走指针,源置空
    Deep &operator=(const Deep &o) { if (this != &o) { delete[] p; p = new char[strlen(o.p) + 1]; strcpy(p, o.p); } return *this; }
    Deep &operator=(Deep &&o) noexcept { if (this != &o) { delete[] p; p = o.p; o.p = nullptr; } return *this; }
    ~Deep() { delete[] p; }
};

int main()
{
    Deep a("hello");
    Deep b = a;                 // 左值 -> 拷贝构造
    Deep c = std::move(a);      // 右值 -> 移动构造
    cout << "a.p 是不是空了: " << (a.p == nullptr) << "\n";
    cout << "b.p = " << b.p << "   c.p = " << c.p << "\n";
}
```

实测输出:

```
a.p 是不是空了: 1
b.p = hello   c.p = hello
```

要点:

- **必须把源的指针置 nullptr**,否则源析构时会把同一块内存再 delete 一次 —— 就是第 9 节那个 double free,只是换了个地方发生
- 参数是 `T &&` 且不加 const(要改源对象);一般标 `noexcept`(容器重新分配时会看这个标记决定"放心移动"还是"老实拷贝")
- 移动只是搬指针,常量时间,和内容多少无关

隐式（自动）生成默认移动构造的条件：

- 没有用户声明的拷贝构造
- 没有用户声明的拷贝赋值运算符
- 没有用户声明的移动赋值运算符
- 没有用户声明的析构函数

```cpp
struct A {
    int x;
    // 没有任何特殊成员函数声明 → 隐式生成移动构造
};

struct B {
    ~B() {}   // 声明了析构
    // → 不生成移动构造！
    // 但拷贝构造仍会生成（为了兼容 C++98）
};
```

实测对照:

```cpp
#include <iostream>
using namespace std;

struct Track
{
    Track()  { cout << "  Track 构造\n"; }
    Track(const Track &)     { cout << "  Track 拷贝构造\n"; }
    Track(Track &&) noexcept { cout << "  Track 移动构造\n"; }
};

struct HasDtor { Track t; ~HasDtor() {} };   // 用户声明了析构 -> 移动构造不再生成
struct NoDtor  { Track t; };                 // 什么都没声明 -> 移动构造自动生成

int main()
{
    cout << "HasDtor:\n";  HasDtor h1; HasDtor h2 = std::move(h1);
    cout << "NoDtor:\n";   NoDtor  n1; NoDtor  n2 = std::move(n1);
}
```

实测输出:

```
HasDtor:
  Track 构造
  Track 拷贝构造        <- 移动构造没生成,std::move 白写了
NoDtor:
  Track 构造
  Track 移动构造
```

要点的反面:**一旦你写了析构函数,编译器就不再生成移动构造**,于是 `std::move` 白写、又走拷贝。想让两者都在,就 `= default` 补上,或者按五法则自己写全。

成员是 `int` / `double` 这种没有资源的类型时,移动 = 拷贝,没有任何好处(第 13 节第 3 条)。

坑:

- 移动之后源对象是"有效但未指定",别再去读它
- 移动构造里忘置空 → double free
- 以为"写了 std::move 就一定走移动":类里压根没有移动构造时,照样走拷贝

## 12. 匿名类对象

匿名对象 = 没有名字的对象，直接写"类名 + 构造实参"造出来，例如 `Cat("Tom")`，也叫临时对象。

三个身份特征：

- 它是右值（绑不进 `Cat &`，能绑 `const Cat &` 和 `Cat &&`）
- 它活到"当前完整表达式"结束，通常就是分号处，不是活到函数结束
- 天生适合"用完就扔"：当实参、当返回值、当初始化源，不用为它起变量名

```cpp
struct Tmp
{
    Tmp(int i) { cout << "构造 " << i << endl; }
    ~Tmp()     { cout << "析构" << endl; }
};

Tmp(1);                  // 构造 → 析构，分号处就销毁了
const Tmp &r = Tmp(2);   // 析构被推迟到 r 的作用域结束（生存期延长）
Tmp &&rr = Tmp(3);       // 右值引用绑定，同样延长
```

绑定规则（本机 g++ 15.2 / C++17 实测）：

| 写法 | 结果 |
| --- | --- |
| `Cat &r = Cat("x");` | × cannot bind non-const lvalue reference to an rvalue |
| `const Cat &r = Cat("x");` | √ 常引用可绑右值，生存期延长到 `r` 的作用域结束 |
| `Cat &&r = Cat("x");` | √ 右值引用可绑右值，同样延长 |
| `Cat *p = &Cat("x");` | × GCC 报错 taking address of rvalue，加 `-fpermissive` 才放过，拿到的是悬垂指针 |
| `void f(Cat &&c);` 然后 `f(c);`（c 是变量） | × cannot bind rvalue reference to lvalue，得写 `f(std::move(c))` |

它和移动构造（第 11 节）的关系：匿名对象是右值，天然优先匹配 `T(T &&)`，而不是 `T(const T &)`。所以"当场造、当场用"的对象能靠移动把资源搬走，不拷贝。C++17 起更彻底——`f(Cat("Tom"))` 直接用实参就地构造形参，实测拷贝 0 次、移动 0 次（C++11 不吃省略优化时为 1 次移动）。

坑：

- 匿名对象活不过分号，别把它的地址或内部指针带出来：`const char *p = string("abc").c_str();` 里 `p` 指向的内存随临时对象一起没了
- C++17 起 `Cat c = Cat("x");` 是就地构造，不产生临时对象；所以别拿"析构调了几次"去猜性能，编译器可能一次都没造临时对象

## 13. std::move

一句话：`std::move` 不移动任何东西，它只把拿到的表达式转成右值引用 `T&&`，好让编译器允许"搬走"。

- 本质就是 `static_cast<remove_reference_t<T> &&>(t)`，声明在 `<utility>` 里，编译期转换，零开销
- 动机：变量是左值，左值只能匹配 `const T&`（→ 拷贝）。想走移动构造/移动赋值，得先把它变成右值，这就是 `std::move` 的活
- 用完之后源对象状态是"有效但未指定"：可以析构、可以重新赋值，但别假设内容还在

```cpp
string a = "hello";
string b = a;             // 拷贝：a 还是 "hello"，b 是另一份
string c = std::move(a);  // 移动：c 拿走 a 的缓冲区，a 通常变空串（标准只保证"有效"）

void f(string &&s);       // 只收右值
// f(a);                  // × a 是左值，编译报错
f(std::move(a));          // √ 显式当右值喂进去
f("tmp");                 // √ 临时对象本来就是右值
```

坑：

1. 对 const 对象 `std::move` 等于白写：`const string s = "x"; string t = std::move(s);` 走的是拷贝构造——`const T&&` 绑不进 `T&&`（要改的权限不够），只剩 `T(const T&)` 这一个候选
2. 别写 `return std::move(局部变量);`，它会挡住 NRVO。实测：`return s;` 拷贝 0 次、移动 0 次；`return std::move(s);` 反而多 1 次移动，而且 GCC 直接给 `-Wpessimizing-move` 警告。返回局部变量/形参时直接 `return s;` 就是最优
3. 移动也是要花钱的，只是便宜：`string`/`vector` 移动是搬指针（常量时间），`int`/`double` 这种"移动"就是拷贝，没有任何好处
4. move 之后再去读源对象是 bug：它只是"有效"，不是"还是老样子"

## 14. `Cat(const std::string &name) : _name(name) {}` 和 `Cat(std::string name) : _name(std::move(name)) {}`

两种存成员的写法：

```cpp
// A 传统：传常引用，成员靠拷贝构造
Cat(const std::string &name) : _name(name) {}

// B 传值再移动：形参自己就是一份现成的对象，再 move 进成员
Cat(std::string name) : _name(std::move(name)) {}
```

按实参类型对比（同场景实测，数字是成员对象的拷贝/移动次数）：

| 实参 | A：`const T&` + 拷贝 | B：传值 + `std::move` |
| --- | --- | --- |
| 左值 `s` | 1 次拷贝 | 1 次拷贝 + 1 次移动 |
| 临时对象 / `std::move(s)` | 1 次拷贝（常引用绑不上右值，只能拷） | 1 次移动（C++11 不吃省略优化时 2 次） |

怎么选：

- 实参经常是右值（字面量、`std::move` 出来的东西、函数返回的临时对象）→ 用 B，能省掉一次真正的深拷贝
- 实参以左值为主 → 用 A，少一次移动
- B 的代价只是多一次移动，而移动很便宜，换来的是代码更简单，不用为同一个参数写 `const&` + `&&` 两组重载。这也是 C++ Core Guidelines（F.15）对"要存下来的参数"的推荐写法
- 其他只读、不存进成员的参数照旧用 `const&`，别到处传值

B 里 `_name(std::move(name))` 的 `std::move` 不能省：形参 `name` 是有名字的变量，是左值，不 move 就会走拷贝构造，前面那次移动就白省了。

上面表格里的拷贝/移动次数来自本机 g++ 15.2 `-std=c++17` 实测：用一个计数类，逐个场景跑 `main` 打印。

## 15. 初始化列表

```cpp
class Point {
public:
    Point(int x, int y);
};

Point p = {1, 2};   // C++11 起，这是隐式转换
```
