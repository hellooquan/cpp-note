# C++ 易错知识点

> 创建时间:2026-09-12
## 目录

- [C++ 易错知识点](#c-易错知识点)
  - [目录](#目录)
  - [1. 加入了string这种数据类型](#1-加入了string这种数据类型)
    - [1.1 高频函数速查](#11-高频函数速查)
    - [1.2 每个函数的最简写法](#12-每个函数的最简写法)
      - [长度与判空](#长度与判空)
      - [取字符](#取字符)
      - [加内容](#加内容)
      - [原地修改](#原地修改)
      - [删除](#删除)
      - [查找](#查找)
      - [截取](#截取)
      - [比较](#比较)
      - [交给 C API 与数字互转](#交给-c-api-与数字互转)
      - [读一整行](#读一整行)
      - [遍历](#遍历)
    - [1.3 九个坑](#13-九个坑)
  - [2. const\_cast,static\_cast,dynamic\_cast](#2-const_caststatic_castdynamic_cast)
    - [2.1 const\_cast](#21-const_cast)
      - [处理常目标指针](#处理常目标指针)
    - [2.2 static\_cast](#22-static_cast)
      - [增加可读性](#增加可读性)
      - [提高安全性](#提高安全性)
    - [2.3 dynamic\_cast](#23-dynamic_cast)
  - [3. 函数重载](#3-函数重载)
    - [3.1 可以形成重载的情形](#31-可以形成重载的情形)
    - [3.2 不可以形成重载的情形](#32-不可以形成重载的情形)
    - [3.3 引用形参](#33-引用形参)
  - [4. 左、右值传递参数，\&和\&\&](#4-左右值传递参数和)
  - [5. 引用\&的本质](#5-引用的本质)
  - [6. 枚举](#6-枚举)
    - [匿名枚举的四种用法](#匿名枚举的四种用法)
    - [枚举能不能当 #define 用](#枚举能不能当-define-用)
    - [枚举的 sizeof:由底层类型决定,不是固定 int](#枚举的-sizeof由底层类型决定不是固定-int)
    - [类内枚举:把枚举定义在类里面](#类内枚举把枚举定义在类里面)
  - [7. 引用返回值解析](#7-引用返回值解析)
  - [8. 异常](#8-异常)
    - [8.1 栈展开：异常穿过函数时发生了什么](#81-栈展开异常穿过函数时发生了什么)
    - [8.2 catch 的匹配规则](#82-catch-的匹配规则)
    - [8.3 别在析构函数里抛异常](#83-别在析构函数里抛异常)
  - [9. 深、浅拷贝](#9-深浅拷贝)
  - [10. 拷贝构造 T(const T \&t)](#10-拷贝构造-tconst-t-t)
  - [11. 移动构造 T(T \&\&t)](#11-移动构造-tt-t)
  - [12. 匿名类对象](#12-匿名类对象)
  - [13. std::move](#13-stdmove)
  - [14. `Cat(const std::string &name) : _name(name) {}` 和 `Cat(std::string name) : _name(std::move(name)) {}`](#14-catconst-stdstring-name--_namename--和-catstdstring-name--_namestdmovename-)
  - [15. 初始化列表](#15-初始化列表)
    - [15.1 类中的 const 成员数据](#151-类中的-const-成员数据)
    - [15.2 初始化列表的书写顺序 ≠ 实际初始化顺序](#152-初始化列表的书写顺序--实际初始化顺序)
    - [15.3 类里直接给成员初值(默认成员初始化器)](#153-类里直接给成员初值默认成员初始化器)
  - [16. 编译期常量:const 与 constexpr](#16-编译期常量const-与-constexpr)
    - [16.1 编译期常量 vs 运行期只读](#161-编译期常量-vs-运行期只读)
    - [16.2 constexpr 有什么用](#162-constexpr-有什么用)
  - [17. 继承 + 动态内存管理(深拷贝遇上继承)](#17-继承--动态内存管理深拷贝遇上继承)
  - [18. 同族类类型转换(上行、下行、对象切片)](#18-同族类类型转换上行下行对象切片)
    - [上行转换:子 → 父,直接赋值就行](#上行转换子--父直接赋值就行)
    - [下行转换:父 → 子](#下行转换父--子)
    - [对象之间的转换 = 切片](#对象之间的转换--切片)
  - [19. 模板 T 的类型匹配与引用折叠](#19-模板-t-的类型匹配与引用折叠)
    - [19.1 四种写法,T 各推成什么](#191-四种写法t-各推成什么)
    - [19.2 引用折叠:引用的引用是谁](#192-引用折叠引用的引用是谁)
    - [19.3 为什么模板里的 T\&\& 左值右值都能接](#193-为什么模板里的-t-左值右值都能接)
    - [19.4 std::forward 为什么必须写 ](#194-stdforward-为什么必须写-)
    - [19.5 auto\&\& 是同一条规则](#195-auto-是同一条规则)
  - [20. 函数模板在什么时候“生成”函数](#20-函数模板在什么时候生成函数)
    - [20.1 生成发生在编译期:`.o` 里已经是机器码](#201-生成发生在编译期o-里已经是机器码)
    - [20.2 一个类型一份,多份靠 weak 符号合并(模板为什么要写 `.h`)](#202-一个类型一份多份靠-weak-符号合并模板为什么要写-h)
    - [20.3 反例:头文件只留声明 → 编译能过,链接才炸](#203-反例头文件只留声明--编译能过链接才炸)
    - [20.4 谁触发实例化:隐式、显式、`extern template`](#204-谁触发实例化隐式显式extern-template)
    - [20.5 编译期“求值”和编译期“生成函数”不是一回事](#205-编译期求值和编译期生成函数不是一回事)
    - [20.6 `static_assert` 只有被实例化时才检查](#206-static_assert-只有被实例化时才检查)
  - [21. 成员函数末尾的 const](#21-成员函数末尾的-const)
    - [21.1 末尾 const 修饰谁](#211-末尾-const-修饰谁)
    - [21.2 为什么参数加了 const,末尾也必须加 —— 四种组合](#212-为什么参数加了-const末尾也必须加--四种组合)
    - [21.3 那按值传参为什么不报错](#213-那按值传参为什么不报错)
    - [21.4 该加的、不该加的](#214-该加的不该加的)

## 1. 加入了string这种数据类型
> 记录于:2026-09-12

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
> 记录于:2026-09-12

- const_cast：专用于去除指针或引用的 const 属性
- static_cast：与旧式转换相近，但提供了更易于查找的语法，并能有效识别不兼容类型
- dynamic_cast：专用于类类型的上下代际间的转换

### 2.1 const_cast

来源:routine/0911/const_cast.cpp

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
> 记录于:2026-09-12

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
> 记录于:2026-09-14

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
- 普通引用绑右值编译报错 `cannot bind non-const lvalue reference of type 'int&' to an rvalue of type 'int'`

## 5. 引用&的本质
> 记录于:2026-09-14

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
> 记录于:2026-09-12

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

enum 是给一组整数起名字的类型:名字在自己这组里唯一,写代码时不用记数字。

定义和取值规则:

```cpp
enum gender { male, female };                       // 不写 = 就从 0 开始递增: male=0, female=1
enum color  { red = 1, green, blue = 10, black };   // 手动给值: green=2(接着上一个 +1), black=11
enum { MAX_N = 8 };                                 // 匿名枚举:只要一组常量,不要类型名
```

`enum gender{male, female} sex;` 这种写法是"定义类型的同时声明一个变量",一句话干两件事:定义类型 gender,再声明一个性别叫 sex 的变量,`sex` 的类型就是 `gender`。

实测:

```cpp
#include <iostream>
using namespace std;

enum gender { male, female } sex;           // 定义类型的同时声明变量
enum color  { red = 1, green, blue = 10, black };

int main()
{
    cout << "male=" << male << " female=" << female << " sizeof(gender)=" << sizeof(gender) << "\n";
    cout << "green=" << green << " black=" << black << "\n";
    sex = female;
    int n = sex;                             // 枚举 -> int:隐式可以
    cout << "sex = " << sex << "   当 int 用 n = " << n << "\n";
    cout << "male + 1 = " << male + 1 << "\n";       // 参与运算就退化成 int
}
```

实测输出:

```
male=0 female=1 sizeof(gender)=4
green=2 black=11
sex = 1   当 int 用 n = 1
male + 1 = 1
```

要点:

- 不给值就从 0 开始,只给第一个值后面自动 +1,也能手动跳(blue = 10)
- 枚举值名直接进外层作用域(unscoped enum):写 `male` 不写 `gender::male`;类型名 `gender` 在 C++ 里不用 typedef 就能直接当类型用
- 枚举值都是编译期常量,所以能当 case 标签、数组长度、结构体里的状态字段
- 枚举 → int 隐式可以;int → 枚举必须显式强转,直接写 `gender sex = 1;` 报错:

```
error: invalid conversion from 'int' to 'gender' [-fpermissive]
```

- sizeof 默认 4(底层当 int 处理);C++11 起可以指定底层类型
- C++11 的 enum class(强枚举)是另一套规矩:

```cpp
#include <iostream>
#include <cstdint>
using namespace std;

enum class Gender : uint8_t { male, female };   // 名字带作用域 + 指定底层类型

int main()
{
    cout << "sizeof(Gender)=" << sizeof(Gender) << "\n";
    cout << "(int)Gender::female=" << (int)Gender::female << "\n";
    Gender g = Gender::male;                     // 必须写作用域,写成 male 不认
    (void)g;                                     // 只为演示,避免未使用变量警告
}
```

```
sizeof(Gender)=1
(int)Gender::female=1
```

想拿它当整数用、或者用整数初始化它,都不行 —— 这就是"强"的地方:

```
error: cannot convert 'Gender' to 'int' in initialization      // int n = g;
error: cannot convert 'int' to 'Gender' in initialization      // Gender h = 0;
```

坑:

- **枚举打印出来是数字不是名字**:`cout << female` 得到 1,名字只存在于源码里。想要名字得自己转:

```cpp
#include <iostream>
using namespace std;

enum gender { male, female };
const char *to_str(gender g) { return g == male ? "male" : "female"; }

int main()
{
    gender sex = female;
    switch (sex) {
        case male:   cout << "男\n"; break;
        case female: cout << "女\n"; break;
    }
    cout << "名字:" << to_str(sex) << "  直接打印:" << sex << "\n";
}
```

```
女
名字:female  直接打印:1
```

- **枚举值名会占外层名字**:同一作用域里两组枚举不能有同名值,也不能再定义同名变量

```
error: 'male' conflicts with a previous declaration          // 第二个 enum 里又写一个 male
error: 'int male' redeclared as different kind of entity     // int male = 3;
```

- **switch 漏 case 编译器会提醒**(unscoped enum 会被检查):

```
warning: enumeration value 'female' not handled in switch [-Wswitch]
```

- 值允许重复,也允许强转塞一个不在枚举里的值(编译放行,逻辑自己负责):`gender g = (gender)99;` 输出 99
### 匿名枚举的四种用法

一句话:匿名枚举就是一组"有名字的编译期常量",不带类型名 —— 只有那些名字能用,它没法拿来声明变量。

用法 1:编译期常量(数组长度、模板参数、case 标签)

```cpp
#include <iostream>
#include <array>
using namespace std;

enum { MAX_N = 8, BUF = 64 };

int main()
{
    int arr[MAX_N];                     // 当数组长度
    for (int i = 0; i < MAX_N; i++) arr[i] = i * i;
    cout << "arr[7]=" << arr[7] << "  sizeof(arr)=" << sizeof(arr) << "\n";

    array<int, MAX_N> a2{};             // 当模板参数
    cout << "a2.size()=" << a2.size() << "\n";

    char buf[BUF] = {0};
    cout << "sizeof(buf)=" << sizeof(buf) << "\n";
}
```

实测输出:

```
arr[7]=49  sizeof(arr)=32
a2.size()=8
sizeof(buf)=64
```

用法 2:位掩码(最实用的一种)

```cpp
enum { RD = 1, WR = 2, EX = 4 };        // 必须是 2 的幂,才能按位组合

int perm = RD | EX;                     // 值自动退化成 int,能直接 |
bool can_write = perm & WR;             // 检查某一位在不在
```

实测(perm = RD | EX):

```
perm=5
有读权限? 有
有写权限? 没有
```

用法 3:类内 / 函数内的小常量(宏做不到的事)

```cpp
#include <iostream>
using namespace std;

class Pool {
    enum { BLOCK = 32, COUNT = 4 };     // 只有这个类的成员函数看得见
public:
    void info() const { cout << "块大小 " << BLOCK << ",共 " << COUNT << " 块\n"; }
};

void f() { enum { LOCAL_MAX = 3 }; cout << "func local " << LOCAL_MAX << "\n"; }
```

实测输出:块大小 32,共 4 块 / func local 3

用法 4:错误码 + switch

```cpp
enum { OK = 0, ERR_OPEN = 1, ERR_READ = 2 };

const char *msg(int code)
{
    switch (code) {
        case OK:       return "成功";
        case ERR_OPEN: return "打不开文件";
        case ERR_READ: return "读失败";
    }
    return "未知";
}
```

匿名枚举、#define、const int 三者的分工:

- `#define MAX 8`:纯文本替换,没有作用域、没有类型,调试器里看不到名字
- `enum { MAX_N = 8 };`:编译期常量,不占存储,连地址都取不到 —— `int *p = &MAX_N;` 报 `error: lvalue required as unary '&' operand`
- `const int C_N = 8;`:也能当数组长度,但它是个真实对象,取地址/绑引用就会实例化一份存储 —— `const int *p = &C_N;` 合法(实测 *p=8)

坑:

- 没有类型名,不能声明变量:`VALUE v;` → `error: 'VALUE' was not declared in this scope`。想要"类型 + 变量"就得起名(`enum gender { male, female } sex;`)
- 值名进所在作用域,重名直接报错:`error: 'MAX_N' conflicts with a previous declaration`
- 位掩码的值必须是 2 的幂(1、2、4、8),写 1、2、3 就会互相重叠
- 值超出 int 范围时编译器会自己挑更大的底层类型(实测 `enum { BIG = 0x100000000LL };` 编得过);想自己定底层类型也行,匿名也能写:`enum : long long { BIG = 5000000000LL };` → 实测 sizeof(BIG)=8

### 枚举能不能当 #define 用

一句话:能替代 #define 里"一个整数常量"这一类(而且是推荐做法),但 #define 另外几类功能枚举一概做不到 —— 分界线是预处理期 vs 编译期。

1) #if / #ifdef 完全看不见枚举(最危险的一条)

```cpp
#include <iostream>
using namespace std;

enum { VERSION = 2 };

int main()
{
#if VERSION >= 2
    cout << "走了新分支\n";
#else
    cout << "走了旧分支:预处理器把没定义的 VERSION 当 0\n";
#endif
}
```

实测输出:走了旧分支:预处理器把没定义的 VERSION 当 0

把第一行换成 `#define VERSION 2`,同一份代码输出变成:走了新分支

枚举是编译期的名字,预处理器根本不认识它 —— `#if` / `#ifdef` 里它按"未定义的标识符 = 0"处理,条件静默不成立。加 -Wundef 才会提醒:

```
warning: 'VERSION' is not defined, evaluates to '0' [-Wundef]
```

2) 枚举装不下非整数,宏什么都能装

```cpp
enum { NAME = "hyq" };
```

```
error: enumerator value for 'NAME' must have integral or unscoped enumeration type
```

宏没这个限制,它只是文本替换:

```cpp
#define MY_TYPE long long      // 类型别名
#define NAME    "hyq"          // 字符串
```

实测输出:NAME=hyq x=1

所以这四类宏能力枚举一个都没有:条件编译、宏函数(如 MAX2(a,b))、字符串常量、类型别名;再加上 # 字符串化、## 拼接、头文件保护宏。

3) 反过来是优势:宏没有作用域,枚举有

```cpp
#define MAX 8
struct S { int MAX = 1; };      // 被替换成 int 8 = 1;
```

```
error: expected unqualified-id before numeric constant
note: in expansion of macro 'MAX'
```

把第一行换成 `enum { MAX = 8 };`,同一段代码正常:实测输出 `s.MAX=1  外面的 MAX=8`

4) 调试信息里的证据(编译带 -g)

```
d7.o(枚举)里搜 ENUM_CONST: 1 次
d8.o(宏)  里搜 MACRO_CONST: 0 次
```

宏的名字在预处理阶段就没了,调试器里看不到。

C++ Core Guidelines 的 Enum.1 就是"优先用枚举代替宏"(理由正是"宏不遵守作用域和类型规则,名字在预处理后消失");同章 Enum.6 又说"避免匿名枚举"(不能声明类型、名字容易撞)—— 所以留着当常量没问题,一旦要传参/声明变量就给它起名。

### 枚举的 sizeof:由底层类型决定,不是固定 int

一句话:不指定底层类型时,底层类型由编译器/ABI 决定,唯一硬要求是"必须装得下所有枚举值";GCC 的习惯是能装进 int 就用 int,装不下就自动换更大的。

实测:

```
sizeof(gender)=4          // 普通枚举,值 0、1
sizeof(small)=4           // 值 1、2、3 —— 不会缩成 1
sizeof(neg)=4             // 有负数 -1
sizeof(uint_e)=4          // 值 0xFFFFFFFF 装不进 int → 变 unsigned int,仍 4
sizeof(big)=8             // 值 0x100000000 → 换成 8 字节的类型
sizeof(E8)=1              // enum E8 : uint8_t —— 自己指定底层类型
sizeof(Gender)=4          // enum class 不给底层类型 → 默认 int
sizeof(G8)=1              // enum class G8 : uint8_t
sizeof(male)=4            // 枚举值的类型就是枚举类型(和 C 不同,C 里枚举值是 int)
sizeof(int)=4
```

同一个程序加 -fshort-enums(让枚举按最小类型存):

```
sizeof(gender)=1  sizeof(small)=1  sizeof(neg)=1
sizeof(uint_e)=4  sizeof(big)=8
sizeof(Gender)=4         // 强枚举默认底层 int,这个开关不改它
```

换架构(aarch64-linux-gnu-gcc,看汇编里 return 的立即数):

```
默认:                       size_gender → mov w0, 4
加 -fshort-enums:           size_gender → mov w0, 1
x86-64 本地默认:             movl $4, %eax
x86-64 本地加 -fshort-enums: movl $1, %eax
```

所以"默认 4 字节"是各编译器/ABI 的共同习惯,不是语言铁律。

为什么要在意 —— 结构体里放枚举,布局跟着底层类型走:

```cpp
#include <cstdint>

enum gender { male, female };           // 底层类型 = int
enum g8 : uint8_t { m8, f8 };           // 底层类型 = uint8_t

struct S1 { char c; enum gender g; };   // 实测 sizeof = 8(c 后面补 3 字节,enum 占 4)
struct S2 { char c; enum g8 g; };       // 实测 sizeof = 2(enum 只占 1)
```

协议帧、寄存器映射、写文件/存 flash 的结构体里放枚举,大小一变数据就错位 —— 这种场合一定要写死底层类型:`enum gender : uint8_t { male, female };`,这样 sizeof 保证 1、和平台无关。

要点:

- 标准口径:不指定底层类型时它由实现选择,必须能表示全部枚举值,而且除非值装不下、否则不大于 int
- 强枚举 enum class 不指定底层类型时固定是 int
- 想要大小可控 → 显式指定底层类型,别指望默认值
- 枚举量(枚举值)的类型是枚举类型本身,只是它到 int 的转换是隐式的,用起来感觉像 int

### 类内枚举:把枚举定义在类里面

一句话:把 `enum` 写进类里,枚举值的名字就被**关进类这一层作用域** —— 类外面访问一律要带类名,换来的是不污染全局名字;枚举只是类型定义,**不占对象空间**。

```cpp
#include <iostream>
using namespace std;

class File
{
public:
    enum Mode { read, write };      // 枚举定义在类里面:名字属于 File 这一层作用域

    void set(Mode m) { _m = m; }
    Mode get() const { return _m; }

private:
    Mode _m = read;                 // 类里面直接用 read,不用写 File::read
};

int main()
{
    File f;
    f.set(File::write);             // 类外面必须带上类名
    cout << "f.get() = " << f.get() << endl;

    cout << "File::read = " << File::read << ", File::write = " << File::write << endl;

    File::Mode m = File::write;     // 枚举类型名也要带类名
    cout << "m = " << m << endl;

    cout << "sizeof(File) = " << sizeof(File) << endl;   // 枚举不占对象空间,只有 _m 那 4 字节

    switch (f.get())                // 枚举是编译期常量,能当 case 标签(在类外面照样要带类名)
    {
    case File::read:  cout << "当前是 read" << endl;  break;
    case File::write: cout << "当前是 write" << endl; break;
    }
}
```

实测输出:

```
f.get() = 1
File::read = 0, File::write = 1
m = 1
sizeof(File) = 4
当前是 write
```

三条读法:

- **名字的归属变了**:普通 `enum` 的枚举值直接进外层作用域(写 `male` 就行),类内枚举的枚举值属于类,类外面必须写 `File::write`;连 `main` 里 `switch` 的 `case` 标签也一样,少写类名就报错:

```cpp
int x = write;     // 类外面直接写 write
```

```
error: ‘write’ was not declared in this scope; did you mean ‘fwrite’?
```

- **枚举类型名也要带类名**:`File::Mode m = File::write;`
- **枚举不占对象空间**:`sizeof(File)` 还是 4(类里只有 `_m` 这一个成员),枚举只是"给一组整数起名字"的类型定义

类里面也放 `enum class`(C++11 强枚举)是同一个道理,只是要写全三段:

```cpp
#include <iostream>
using namespace std;

class File
{
public:
    enum class Mode { read, write };

    void set(Mode m) { _m = m; }
    Mode get() const { return _m; }

private:
    Mode _m = Mode::read;              // 强枚举连类里面也要写枚举名
};

int main()
{
    File f;
    f.set(File::Mode::write);          // 三段:类名::枚举名::值

    cout << "f.get() == File::Mode::write : " << (f.get() == File::Mode::write) << endl;
    cout << "sizeof(File::Mode) = " << sizeof(File::Mode) << endl;
    cout << "(int)File::Mode::write = " << (int)File::Mode::write << endl;
}
```

实测输出:

```
f.get() == File::Mode::write : 1
sizeof(File::Mode) = 4
(int)File::Mode::write = 1
```

强枚举少写一段不行,报错原文:

```cpp
class File { public: enum class Mode { read, write }; };

int main()
{
    File::Mode m = File::write;    // 想要 File::Mode::write
    (void)m;
}
```

```
error: ‘write’ is not a member of ‘File’
```

顺带一个实用的:底层类型会决定"当成员时占多少字节"(接上面 `枚举的 sizeof` 那条):

```cpp
#include <iostream>
using namespace std;

class F1 { public: enum Mode { a, b };                      private: Mode m = a; };
class F2 { public: enum Mode : unsigned char { a, b };      private: Mode m = a; };

int main()
{
    cout << "sizeof(F1) = " << sizeof(F1) << ", sizeof(F1::Mode) = " << sizeof(F1::Mode) << endl;
    cout << "sizeof(F2) = " << sizeof(F2) << ", sizeof(F2::Mode) = " << sizeof(F2::Mode) << endl;
}
```

实测输出:

```
sizeof(F1) = 4, sizeof(F1::Mode) = 4
sizeof(F2) = 1, sizeof(F2::Mode) = 1
```

坑:

- 类内普通枚举的名字**只在类里**能省类名,类外(包括 `main` 里 `switch` 的 `case`)都要带 —— 报错原文上面两条
- 别以为"类里加了枚举,对象就变大":枚举不占空间;只有当它是**成员变量**、或者底层类型被指定时,才按底层类型的大小算(`F2` 因为指定了 `unsigned char`,整个对象只有 1 字节)
- `enum class` 必须写全 `类名::枚举名::值`,而且在类里也不能省枚举名(`Mode::read`);普通类内枚举在类里可以省(`read`)
- `enum class` 不能隐式转成 `int`(要打印得写 `(int)File::Mode::write`),普通类内枚举可以

## 7. 引用返回值解析
> 记录于:2026-09-12

来源:routine/0915/Counter &.cpp

一句话:返回**引用**=把原件交给你,返回**值**=给你一份拷贝。区别落在三点:能不能改原件、有没有拷贝开销、会不会悬垂。

正确用法(返回的东西活得比你函数长):

⭐ 重点

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
> 记录于:2026-09-12

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
> 记录于:2026-09-14

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
> 记录于:2026-09-14

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
> 记录于:2026-09-14

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
> 记录于:2026-09-14

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

绑定规则：

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
> 记录于:2026-09-14

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
> 记录于:2026-09-14

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


## 15. 初始化列表
> 记录于:2026-09-14

```cpp
class Point {
public:
    Point(int x, int y);
};

Point p = {1, 2};   // C++11 起，这是隐式转换
```

### 15.1 类中的 const 成员数据

一句话:const 成员是"每个对象出生时登记一次"的数据,登记完就锁死;而且这次登记只能发生在初始化列表里。

```cpp
#include <iostream>
#include <string>
using namespace std;

class Student {
public:
    const int id;                // const 成员:出生就定死
    string name;
    mutable int look_times = 0;  // mutable 是唯一例外

    Student(int i, string n) : id(i), name(n) {}   // 唯一一次给值的机会

    void show() const
    {
        look_times++;            // const 成员函数里改 mutable 成员:合法
        cout << "id=" << id << " name=" << name << " 查了 " << look_times << " 次\n";
    }
};

int main()
{
    Student s(2026, "hyq");
    s.show();
    s.show();
}
```

实测输出:

```
id=2026 name=hyq 查了 1 次
id=2026 name=hyq 查了 2 次
```

三条铁律(编译器逐个把关):

1. 只能在初始化列表里给值,搬进构造函数体就编不过:

```cpp
struct A { const int id; A(int v) { id = v; } };
```

```
error: uninitialized const member in 'const int' [-fpermissive]
note: 'const int A::id' should be initialized
error: assignment of read-only member 'A::id'
```

gcc 一次报两条,其实说的是同一件事:构造函数体里那句 `id = v` 是"赋值",而 const 成员的第一次给值只认初始化列表 —— 初始化列表里没给(第一条"没初始化"),函数体里又想改(第二条"只读成员不能赋值")。

2. 忘了给值,构造函数本身就不合法:

```cpp
struct A { const int id; A() {} };
```

```
error: uninitialized const member in 'const int' [-fpermissive]
note: 'const int A::id' should be initialized
```

3. 对象构造完再改:

```cpp
struct A { const int id; A(int v) : id(v) {} };
int main() { A a(1); a.id = 2; }
```

```
error: assignment of read-only member 'A::id'
```

两个补充:

- 可以在类里直接给默认值,而且只有初始化列表能覆盖它(const 成员唯一允许"再给一次"的地方):

```cpp
#include <iostream>
using namespace std;

struct A {
    const int id = 0;            // 类内默认值:每个对象默认 0
    A() = default;
    A(int v) : id(v) {}          // 初始化列表覆盖它
};

int main() { A a, b(7); cout << "a.id=" << a.id << "  b.id=" << b.id << "\n"; }
```

```
a.id=0  b.id=7
```

- mutable 是唯一的开口:const 成员函数里只允许改 mutable 成员(上面 look_times 就是靠它自增的)

最大的坑(连带后果):

- **一个 const 成员会把整个类的拷贝赋值运算符删掉**,`a = b` 直接编不过:

```cpp
struct A { const int id; A(int v) : id(v) {} };
int main() { A a(1), b(2); a = b; }
```

```
error: use of deleted function 'A& A::operator=(const A&)'
note: 'A& A::operator=(const A&)' is implicitly deleted because the default definition would be ill-formed
```

影响:这样的类不能整体赋值,也就不能用在需要赋值的场景(排序、erase、按值交换)。所以加 const 成员前先问一句"这个类以后还要不要整体赋值";只是想"字段不给外面改",用 private + 只给 getter 更划算,不牺牲赋值能力。

- static const 成员是另一回事:它属于类不属于对象,所有对象共用一份,整型可以类内直接给值:

```cpp
#include <iostream>
using namespace std;

struct A {
    static const int MAX = 100;
    const int id;
    A(int v) : id(v) {}
};

int main() { cout << "A::MAX=" << A::MAX << "\n"; }
```

```
A::MAX=100
```

记忆锚点:const 成员 = 出生登记一次的身份证号 —— 登记地点只能是初始化列表,登记完谁也不能改,代价是整个类失去赋值能力。

### 15.2 初始化列表的书写顺序 ≠ 实际初始化顺序

一句话:成员按**声明顺序**初始化,初始化列表里怎么写都改不了它。

```cpp
struct A {
    int a; int b;
    A(int x) : b(x), a(b + 1) {}   // 看着先给 b,实际 a 先初始化
};
```

g++ 会直接点出来:

```
warning: 'A::b' will be initialized after [-Wreorder]
warning: member 'A::b' is used uninitialized [-Wuninitialized]
```

实测 `A o(10);` 输出:

```
a=1 b=10
```

a 里面用的 b 此时还没赋值,读到的是垃圾值(这次恰好是 1)。做法:初始化列表按声明顺序写,或者用参数算,别去读另一个成员。

### 15.3 类里直接给成员初值(默认成员初始化器)

一句话:C++11 起,成员可以在**类里直接写个初值**(`int x = 1;`),构造函数没管这个成员时它生效;构造函数初始化列表里给了值,类里这个就被忽略。优先级:**初始化列表 > 类里给的初值 > 什么都没给**。

```cpp
#include <iostream>
#include <string>
using namespace std;

enum level { high, medium, low };

class Item
{
public:
    int         i   = 42;                 // int
    double      d   = 3.14;               // double
    string      s   = "hello";            // 类对象
    int         arr[3] = {1, 2, 3};       // 数组
    level       lv  = medium;             // 枚举(和上面几种没区别)
    const int   ci  = 7;                  // const 成员
    int        *p   = nullptr;            // 指针

    void show() const
    {
        cout << i << " " << d << " " << s << " " << arr[0] << arr[1] << arr[2]
             << " lv=" << lv << " ci=" << ci << " p=" << (p ? "非空" : "空") << endl;
    }
};

int main()
{
    Item it;
    it.show();
    cout << "sizeof(Item) = " << sizeof(Item) << endl;
}
```

实测输出:

```
42 3.14 hello 123 lv=1 ci=7 p=空
sizeof(Item) = 80
```

谁覆盖谁,一个类里就能试全:

```cpp
#include <iostream>
using namespace std;

class A
{
public:
    int x = 1;                        // 类里给的初值

    A() {}                            // 构造函数不管 x:用 1
    A(int v) : x(v) {}                // 初始化列表给值:类里的 1 被忽略
    A(int a, int b) { x = a + b; }    // 构造函数体里赋值:先默认成 1,再改成 a+b
};

int main()
{
    A a1;
    A a2(100);
    A a3(3, 4);
    cout << "a1.x = " << a1.x << ", a2.x = " << a2.x << ", a3.x = " << a3.x << endl;
}
```

实测输出:

```
a1.x = 1, a2.x = 100, a3.x = 7
```

`const` 成员也是同一套(这就是 15.1 那条的完整版,`const` 成员不一定非得靠初始化列表):

```cpp
#include <iostream>
using namespace std;

class A
{
public:
    const int a = 1;          // 类里的默认值(登记一次)

    A() {}                    // 构造函数不管它:用默认的 1
    A(int v) : a(v) {}        // 初始化列表给值:顶掉类里的默认值
};

int main()
{
    A d;
    A c(9);
    cout << "默认构造出来的 a = " << d.a << endl;
    cout << "A(9) 构造出来的 a = " << c.a << endl;
}
```

实测输出:

```
默认构造出来的 a = 1
A(9) 构造出来的 a = 9
```

两种写法错的对照:

```cpp
class A
{
public:
    const int a = 1;

    A(int v) { a = v; }       // 构造函数体里给 const 成员赋值
};
```

```
error: assignment of read-only member ‘A::a’
```

```cpp
class A
{
public:
    const int a;              // 类里不给默认值,A() {} 的初始化列表里也不给

    A() {}
};
```

```
error: uninitialized const member in ‘const int’ [-fpermissive]
```

三条规矩:

- **初始化列表 > 类里初值 > 什么都没给**:谁离构造函数更近谁说了算;构造函数体里的赋值是"先按初值初始化好,再改一遍"
- **按成员声明顺序执行(和 15.2 是同一个规矩)**:类里给初值也一样,引用后面的成员读到的是未初始化内存 —— 实测 `class B { int y = x + 1; int x = 1; };` 打出 `y = 1`(`x` 那格的原始字节恰好是 0),**gcc 一句警告都不给**
- 类里给的初值**不占额外空间**:`sizeof` 只按成员本身的类型算,初值只是每个构造函数里自动多出来的一句初始化

不行的几种:

```cpp
class A
{
public:
    auto x = 1;            // error: non-static data member declared with placeholder ‘auto’
    static int z = 5;      // error: ISO C++ forbids in-class initialization of non-const static member ‘A::z’
};
```

`int x = 1;` 这行在**老标准里根本不存在**,`-std=c++98` 编就是:

```
error: non-static data member initializers only available with ‘-std=c++11’ or ‘-std=gnu++11’
```

坑:

- 它是"默认值"不是"赋值":初始化列表里给了值,类里写的那个就作废,别以为类里的值一定生效
- 构造函数**体**里给 `const` 成员赋值 = 编译错误(只能写在初始化列表里);但"类里给初值 + 初始化列表再给值"是合法的 —— 初始化列表赢
- 成员之间互相引用要盯着声明顺序:顺序反了编译器不报错,值却是不确定的
- `static` 成员(C++17 之前)不能这样给值;`auto` 不能当成员类型
- 老教材里看不到这个写法,因为它是 C++11 才加的(`-std=c++11` 起)

## 16. 编译期常量:const 与 constexpr
> 记录于:2026-09-15

### 16.1 编译期常量 vs 运行期只读

标准里没有"运行时常量"这个词。C++ 里要分的是两件事:编译期常量(标准术语是"常量表达式")和运行期只读(const 变量,值要等程序跑起来才知道);口语里说的"运行时常量",准确说法就是"只读变量"。

一句话:const 只回答"能不能改",不回答"什么时候知道值"。看一个 const 到底是哪一种,只看它的初始化器。

实测 1:值在运行期才定,const 也救不了

```cpp
#include <iostream>
using namespace std;

int main()
{
    int n; cin >> n;
    const int N = n;                        // 只读,不是常量
    switch (n) { case N: break; }
}
```

```
error: the value of 'N' is not usable in a constant expression
note: 'N' was not initialized with a constant expression
```

同一份代码里 `int arr[N];` 这行 g++ 只给个 unused 警告 —— 那是变长数组(VLA),g++ 当扩展放过,标准 C++ 不允许。加 -pedantic-errors 就露馅:

```
error: ISO C++ forbids variable length array 'arr' [-Wvla]
```

实测 2:初始化器是常量的 const,就是货真价实的编译期常量

```cpp
#include <iostream>
using namespace std;

const int M = 5;
constexpr int K = M + 1;
enum { E = 8 };

int main()
{
    int a1[M], a2[K], a3[E];                        // 三个都能当数组长度
    int n = 1;
    switch (n) { case M: case K: case E: break; }   // 也都能当 case 标签
    cout << "sizeof(a1)=" << sizeof(a1) << " a2=" << sizeof(a2) << " a3=" << sizeof(a3) << "\n";
}
```

```
sizeof(a1)=20 a2=24 a3=32
```

实测 3:两者在运行期的差别,看目标文件最直观

```
只读变量占一块真实存储:
  _ZL12GLOBAL_CONST    4 OBJECT  LOCAL  DEFAULT  5        ← 在 .rodata 段
枚举值完全不占存储:
  int *p = &ENUM_CONST;
  error: lvalue required as unary '&' operand
```

实测 4:字符串字面量是运行期只读数据(.rodata,程序跑起来才有那块内存)

```cpp
int main() { char *s = (char *)"hi"; s[0] = 'H'; }
```

→ Segmentation fault,退出码 139

要点:

- const 管"能不能改",constexpr 管"是不是编译期就知道值",两件事
- 判断一个 const 变量属于哪一种,只看初始化器:字面量/常量表达式 → 编译期常量;函数返回值、输入、别的变量 → 运行期只读
- 枚举值、字面量、constexpr 变量不占存储(能当立即数、能进模板参数);只读变量占一份内存(.rodata/flash),好处是能取地址、能当数组传
- C++20 的 constinit 是第三件事:只管"静态初始化",不保证是编译期常量

### 16.2 constexpr 有什么用

一句话:constexpr = "这个能在编译期算"的承诺。给变量用就是编译期常量;给函数用就是"能编译期算、也能当普通函数跑"的函数 —— 能算的场景编译期算完,算不了(参数运行期才知道)就照常当函数用。

用处 1:把计算从运行期挪到编译期,算好的值直接变立即数

```cpp
constexpr int fact(int n) { return n <= 1 ? 1 : n * fact(n - 1); }

int a_fact5(void) { return fact(5); }   // 实参是常量
int b_fact(int n) { return fact(n); }   // 实参运行期才知道
```

实测汇编(-O2):

```
a_fact5 的函数体:  movl $120, %eax      ← 5! 直接算好
                  ret
b_fact:            imull %edi, %eax
                   subl $1, %edi
                   jne .L5              ← 老老实实跑循环
```

用处 2:必须编译期算的地方只有它填得上(数组长度、模板参数、case 标签、static_assert)

```cpp
constexpr int fact(int n) { return n <= 1 ? 1 : n * fact(n - 1); }
int table[fact(4)];
```

实测目标文件里 table 大小 = 96 字节 = 24 个 int = 4!。

用处 3:替代宏函数

```cpp
#define SQ_BAD(x) x*x
constexpr int sq(int x) { return x * x; }
```

实测 `SQ_BAD(1+2)` = 5(被替换成 1+2*1+2),`sq(1+2)` = 9;而且 `int arr[sq(3)];` 合法(实测 sizeof(arr)=36)。宏还有"没类型检查、调试器看不见、不受命名空间管"这些毛病,constexpr 函数一个都没有。

用处 4:编译期对象 + 编译期断言

```cpp
struct Point {
    int x, y;
    constexpr Point(int x, int y) : x(x), y(y) {}
    constexpr int sum() const { return x + y; }
};

constexpr Point P{1, 2};
static_assert(P.sum() == 3, "编译期就把错挡下来");
int table[P.sum()];                     // 实测 sizeof(table)=12
```

用处 5:现代版编译期计算,不用再写模板特化递归

```cpp
#include <type_traits>
using namespace std;

constexpr int fib(int n)                // C++14 起循环、局部变量都能写
{
    int a = 0, b = 1;
    for (int i = 0; i < n; i++) { int t = a + b; a = b; b = t; }
    return a;
}
static_assert(fib(10) == 55);

template <typename T> const char *describe(T)
{
    if constexpr (is_integral_v<T>) return "整数";   // 编译期分支,另一条不生成代码
    else                            return "别的";
}
```

实测输出:describe(1)=整数  describe(1.5)=别的

用处 6:错误在编译期就被挡下来

```cpp
constexpr int div0(int a, int b) { return a / b; }
constexpr int X = div0(1, 0);
```

```
error: '(1 / 0)' is not a constant expression
```

坑:

- constexpr 函数是"能编译期算",不是"一定编译期算";只有常量语境(数组长度、模板参数、static_assert、constexpr 变量初始化)才是硬保证
- 反证:-O0 下 `a_fact5()` 的汇编还是 `movl $5, %edi; call _Z4facti`,到 -O2 才变成 `movl $120, %eax` —— 普通上下文里提前算不算取决于优化器,别以为标了 constexpr 就一定零开销
- 参数来自运行期的调用,这个函数就是普通函数
- 各版本逐步放宽:C++11 函数体只能一条 return,C++14 加循环和局部变量,C++17 加 if constexpr,C++20 才允许 new/虚函数/try
- constexpr 变量隐含 const、必须初始化;C++17 起它是 inline 的(放头文件里多份包含不会重复定义)
- 别和 constinit 混:constinit 只管静态初始化,不保证编译期常量

记忆锚点:constexpr 就是把活挪到编译时 —— 该算的值提前算成常量,必须编译期算的地方填得上,顺手替掉宏函数,还能在编译期抓错。

## 17. 继承 + 动态内存管理(深拷贝遇上继承)
> 记录于:2026-09-15

来源:routine/0915/继承动态内存管理.cpp

一句话:派生类里有 `new` 出来的成员时,"三件事"都要分两半做 —— 基类那半显式交给基类的对应函数(拷贝构造 `: Person(r)`,拷贝赋值 `Person::operator=(r)`),自己那半自己管;基类析构再加 `virtual`,这一套才完整。

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class Person
{
    char *_name;

public:
    Person(const char *name = nullptr)
    {
        if (name != nullptr)
        {
            _name = new char[strlen(name) + 1];   // new 的字节数永远是 strlen + 1
            strcpy(_name, name);
        }
        else
            _name = nullptr;
    }

    Person(const Person &r)                        // 参数必须 const 引用,才能拷 const 对象和临时对象
    {
        if (r._name != nullptr)
        {
            _name = new char[strlen(r._name) + 1];
            strcpy(_name, r._name);
        }
        else
            _name = nullptr;
    }

    Person &operator=(const Person &r)
    {
        if (this == &r)                            // 自赋值直接返回,否则下面会先把源头删掉
            return *this;

        char *tmp = nullptr;                       // 先把新数据准备好,再删旧的
        if (r._name != nullptr)
        {
            tmp = new char[strlen(r._name) + 1];
            strcpy(tmp, r._name);
        }
        delete[] _name;
        _name = tmp;

        return *this;
    }

    virtual ~Person() { delete[] _name; }          // 基类析构必须 virtual:delete 基类指针时派生类析构才会跑

    void show() const
    {
        cout << "姓名: " << (_name ? _name : "(空)") << ",";   // 空指针要判,cout << nullptr 会把流弄坏
    }
};

class Student : public Person
{
    char *_id;

public:
    Student(const char *name, const char *id = nullptr)
        : Person(name)
    {
        if (id != nullptr)
        {
            _id = new char[strlen(id) + 1];
            strcpy(_id, id);
        }
        else
            _id = nullptr;
    }

    Student(const Student &r)
        : Person(r)                                // 基类那半交给基类的拷贝构造
    {
        if (r._id != nullptr)
        {
            _id = new char[strlen(r._id) + 1];
            strcpy(_id, r._id);
        }
        else
            _id = nullptr;
    }

    Student &operator=(const Student &r)
    {
        if (this == &r)                            // 派生类这半也要自己判一次
            return *this;

        Person::operator=(r);                      // 不写这句,基类的 _name 就漏拷贝
        delete[] _id;
        if (r._id != nullptr)
        {
            _id = new char[strlen(r._id) + 1];
            strcpy(_id, r._id);
        }
        else
            _id = nullptr;

        return *this;
    }

    ~Student() { delete[] _id; }

    void show() const
    {
        Person::show();
        cout << "学号: " << (_id ? _id : "(空)") << endl;
    }
};

int main(int argc, char const *argv[])
{
    Student s1("Jack", "100");
    s1.show();

    Student s2(s1);      // 拷贝构造
    s2.show();

    s2 = s1;             // 拷贝赋值
    s2.show();

    s1 = s1;             // 自赋值
    s1.show();

    Student s3("Tom");   // id 用默认值 nullptr
    s3.show();
    cout << "上面这行之后输出还在:说明流没被搞坏" << endl;

    Person *p = new Student("Amy", "202");
    p->show();           // show() 不是虚函数,走基类版本,只打印姓名
    cout << endl;
    delete p;            // 有虚析构,派生类那半才会一起释放

    return 0;
}
```

实测输出:

```
姓名: Jack,学号: 100
姓名: Jack,学号: 100
姓名: Jack,学号: 100
姓名: Jack,学号: 100
姓名: Tom,学号: (空)
上面这行之后输出还在:说明流没被搞坏
姓名: Amy,
```

七行依次是:直接构造、拷贝构造、拷贝赋值、自赋值、id 用默认值 nullptr、判空之后流没被搞坏、基类指针调用非虚的 show()。

要点:

- 派生类拷贝构造的初始化列表必须写 `: Person(r)`:不写就是调用基类的默认构造 —— 基类没有默认构造直接编不过,有的话基类成员就是没被拷贝
- 派生类拷贝赋值里必须写一句 `Person::operator=(r);`:不写这句,基类的 `_name` 漏拷贝
- 自赋值检查要写两处(基类 `operator=` 一处,派生类 `operator=` 一处):`if (this == &r) return *this;`。赋值的第一步就是 `delete[]`,不挡自赋值就会先把源头删掉,再去读已经释放的内存
- 赋值的顺序:先把新数据申请到临时指针,成功之后再 `delete[]` 旧的
- `new char[strlen(s) + 1]`:strcpy 连结尾的 `'\0'` 一起写,少写 1 个字节就是堆越界,而且这种越界不一定当场崩,会静默踩坏堆
- 参数一律写 `const T &`:非 const 引用既接不了 const 对象,也接不了函数返回的临时对象
- 基类析构写 `virtual`:只要有多态删除(`Base *p = new Derived; delete p;`),不写 virtual 就只跑基类析构,派生类的堆成员泄漏
- 打印 `char *` 成员之前判空:`cout << nullptr` 不崩,但会把 cout 置成失败状态,之后所有输出静默消失(比段错误更难查)
- `show()` 不加 `virtual` 的话,`Person *p = new Student; p->show()` 走的是基类版本(实测只打印姓名);要按运行时类型调用就得加 virtual
- 三法则:自己写了析构的类,拷贝构造和拷贝赋值通常也得自己写;再把移动构造、移动赋值补上就是五法则(第 11 节)

记忆锚点:派生类的三件事都"分两半" —— 基类那半用 `Person(r)` / `Person::operator=(r)` 显式交出去,自己那半自己 new/delete;基类析构加 `virtual`,`delete 基类指针` 才安全。

## 18. 同族类类型转换(上行、下行、对象切片)
> 记录于:2026-09-16

来源:routine/0915/同族类.cpp

一句话:同族类 = 同一条继承链上的类(父类、子类、以及父类的父类……)。它们之间的转换只有两个方向 —— **子 → 父(上行)是隐式的、永远安全;父 → 子(下行)编译器不认,必须写显式转换,而且写错了它也不拦**。

两个方向一张表:

| 方向 | 叫法 | 怎么写 | 谁检查 | 结果 |
| --- | --- | --- | --- | --- |
| 子 → 父 | 上行转换 | `A *pa = &b;`(直接赋值,也可以 `static_cast`) | 编译期自动完成,不用写转换 | 安全 |
| 父 → 子 | 下行转换 | `static_cast<B *>(pa)` | 没人查 | 真身不对就等着出事 |
| 父 → 子 | 下行转换 | `dynamic_cast<B *>(pa)` | 运行期查真身 | 真身不对给 `nullptr` / 抛 `bad_cast` |
| 子对象 → 父对象 | 对象切片 | `A a = b;` | 编译期允许,不报错 | 子类那半被切掉,数据没了 |

### 上行转换:子 → 父,直接赋值就行

```cpp
#include <iostream>
using namespace std;

class A
{
public:
    int x = 1;
    void fa() { cout << "A::fa 被调用" << endl; }
};

class B : public A
{
public:
    int y = 2;
    void fb() { cout << "B::fb 被调用" << endl; }
};

int main()
{
    B b;
    b.x = 10;
    b.y = 20;

    A *pa = &b;                            // 上行:子类指针 -> 父类指针
    A &ra = b;                             // 上行:子类对象 -> 父类引用

    cout << "pa->x = " << pa->x << endl;   // 输出: pa->x = 10
    pa->fa();                              // 输出: A::fa 被调用
    cout << "ra.x = " << ra.x << endl;     // 输出: ra.x = 10

    cout << "&b = " << (void *)&b << ", pa = " << (void *)pa << endl;
    // 输出: &b = 0x7ffe635d1870, pa = 0x7ffe635d1870
    //(每次运行地址都不一样,关键是这两个数永远相同:单继承下转父类指针不搬地址)
}
```

为什么不用写转换:子类对象里面本来就**完整地含着一块父类子对象**,父类指针只是换了个视角去看这块内存,不会看到不存在的东西。

代价是父类指针**只能看见父类那半**:

```cpp
pa->y = 1;   // error: ‘class A’ has no member named ‘y’
pa->fb();    // 同理,编译不过
```

但有一件事会跟着真身走 —— **虚函数**(多态):

```cpp
#include <iostream>
using namespace std;

class A { public: virtual void who() { cout << "我是 A" << endl; } };
class B : public A { public: void who() override { cout << "我是 B" << endl; } };

int main()
{
    B b;
    A *pa = &b;
    pa->who();   // 输出: 我是 B   —— 指针类型是 A,真正被调用的还是 B 的版本
}
```

### 下行转换:父 → 子

三层,一层比一层多花代价:直接赋值(编译期就不让)→ `static_cast`(编译期硬转,不看真身)→ `dynamic_cast`(运行期查真身)。

先记住类长什么样(`virtual` 是 `dynamic_cast` 的前提):

```cpp
class Base
{
public:
    int x = 1;
    virtual ~Base() {}      // 有虚函数 = 多态类型
};

class Derived : public Base
{
public:
    int y = 2;
};
```

**一、直接赋值:编译期就不让**

```cpp
Base *p = &d;
Derived *q = p;   // error: invalid conversion from ‘Base*’ to ‘Derived*’ [-fpermissive]
```

编译器的态度很明确:手里只有一个父类指针,它无法确认后面那半(子类新增成员)到底在不在。

**二、`static_cast`:编译期硬转,不看真身 —— 危险就在这**

```cpp
Base b;
Base *p2 = &b;                             // 真身是 Base,不是 Derived
Derived *s = static_cast<Derived *>(p2);   // 照样转成功
cout << (s != nullptr) << endl;            // 输出: 1
cout << "s->y = " << s->y << endl;         // 输出: s->y = 0
```

那个 `0` 不是"没找到、返回 0"的意思 —— `Base` 里根本没有 `y` 那块内存,这个 `0` 是从 `b` 后面读到的**别的内存**。危险点正在这里:越界读往往给一个看着很正常的值,不崩、不报错,结果悄悄错。

写进去就更明显,ASAN 能当场抓住(`Derived` 里加了一个 `long long y[8]`):

```cpp
Base *p = new Base;
static_cast<Derived *>(p)->y[3] = 7;
```

```
ERROR: AddressSanitizer: heap-buffer-overflow
WRITE of size 8 at 0x6d7e914e0038 thread T0
...
0x6d7e914e0038 is located 24 bytes after 16-byte region [0x6d7e914e0010,0x6d7e914e0020)
```

那块内存只有 16 字节(就是 `Base` 的大小),按 `Derived` 的布局去写,自然写到外面去了。

**三、`dynamic_cast`:带真身检查的转型**

```cpp
Base *p1 = &d;   // 真身是 Derived
Base *p2 = &b;   // 真身是 Base

cout << (dynamic_cast<Derived *>(p1) ? "成功" : "失败") << endl;
// 输出: 成功
cout << (dynamic_cast<Derived *>(p2) ? "成功" : "失败(nullptr)") << endl;
// 输出: 失败(nullptr)
```

引用版没有"空引用"这种东西,失败只能抛:

```cpp
try
{
    Derived &r = dynamic_cast<Derived &>(*p2);
    (void)r;
}
catch (const bad_cast &e)
{
    cout << e.what() << endl;   // 输出: std::bad_cast
}
```

机制、前提(类必须是多态的)和坑,第 2.3 节已经写过,这里不重复;`static_cast` 和它的差别就是:多花的那点运行期时间,买的是"查真身"这一步。

### 对象之间的转换 = 切片

指针和引用只是换个视角看**同一个对象**,对象之间赋值则是真的**拷一份**,这时子类那半会被切掉:

```cpp
#include <iostream>
using namespace std;

class A
{
public:
    int x = 1;
    virtual void who() { cout << "我是 A" << endl; }
};

class B : public A
{
public:
    int y = 2;
    void who() override { cout << "我是 B" << endl; }
};

void talk(A a)      // 形参按值收父类:传子类对象进来也会被切
{
    a.who();
}

int main()
{
    B b;
    b.x = 10;
    b.y = 20;

    A *pa = &b;
    pa->who();      // 输出: 我是 B   —— 指针还指着那个 B 对象
    A &ra = b;
    ra.who();       // 输出: 我是 B   —— 引用一样

    A acopy = b;    // 对象赋值:只把父类那半拷过来
    cout << "acopy.x = " << acopy.x << endl;   // 输出: acopy.x = 10
    acopy.who();    // 输出: 我是 A   —— acopy 自己已经是 A 对象了,多态没了

    talk(b);        // 输出: 我是 A   —— 按值传参 = 又切了一次
    return 0;
}
```

一句话:**指针和引用不切(还指向原对象),按值拷贝才切**。

反着来(父对象 → 子对象)同样不认:

```cpp
A a;
B bcopy = a;   // error: conversion from ‘A’ to non-scalar type ‘B’ requested
B *pb = &a;    // error: invalid conversion from ‘A*’ to ‘B*’ [-fpermissive]
```

`A a = b;` 之后想再读 `a.y` 也读不到:

```cpp
cout << a.y << endl;   // error: ‘class A’ has no member named ‘y’
```

坑:

- 把 `static_cast` 当"向下转型"用:它编译期硬转、不看真身,真身不对照样给你一个非空指针,错误会拖到运行期,以"越界读 / 乱值 / 段错误"的形式出现。下行转换要么先确认真身,要么用 `dynamic_cast`。
- 判断转型成没成功别只看指针空不空:`static_cast` 出来的**永远非空**(上面转出来的就是 1),只有 `dynamic_cast` 的 `nullptr` 才是"真身不对"的信号。
- 能直接赋值的地方别写 `dynamic_cast`:上行转换是免费的,不需要任何转换语法。
- 切片不报错、不崩溃,只是数据悄悄少了:子类新增的成员在父类对象里根本不存在,虚函数也不再是子类版本。函数按值收父类对象(`void f(A a)`)是切片最常见的现场,想保留多态就收引用或指针(`void f(const A &a)`)。
- "`sizeof` 没变"不能证明没切片:`sizeof(A) = 16`、`sizeof(B) = 16`,子类新增的 `int y` 正好塞进了父类的填充空隙,大小一样,但 `y` 确实被切掉了。
- 单继承时父子指针地址相同(`&b` 和 `pa` 打出来是一个地址),**多重继承下不一定**:父类子对象不在对象开头,转父类指针时会加偏移 —— `struct D : A, B` 的 `D x`,`A *pa = &x` 得到 `0x…13c`,而 `B *pb = &x` 得到 `0x…140`,差了 4 字节。先记住单继承的结论,多重继承的等后面用到再说。

## 19. 模板 T 的类型匹配与引用折叠
> 记录于:2026-09-16

一句话:模板里的 `T` 不是你自己指定的,是编译器拿**实参**反推出来的;形参写成 `T` / `T&` / `const T&` / `T&&` 会各推出一套结果,而模板里的 `T&&` 之所以左值右值都能接,靠的就是**引用折叠**。

### 19.1 四种写法,T 各推成什么

| 形参写法 | 传左值 `int i` | 传右值 `3` | 传 `const int ci` | 想表达的意思 |
| --- | --- | --- | --- | --- |
| `byVal(T t)` | `T = int` | `T = int` | `T = int` | 按值:引用和顶层 const 全被剥掉 |
| `byRef(T &t)` | `T = int` | 编译不过 | `T = const int` | 只接左值,实参的 const 会留在 T 里 |
| `byCRef(const T &t)` | `T = int` | `T = int` | `T = int` | 左右值都接,const 是形参自带的,不占 T |
| `byFwd(T &&t)` | `T = int&` | `T = int` | `T = const int&` | 转发引用:只有它会把 T 推成引用类型 |

表里每格都是 `T` 的真身,直接看 `__PRETTY_FUNCTION__` 打出来的原文:

```cpp
#include <iostream>
using namespace std;

template <typename T> void byVal(T t)         { cout << "byVal  " << __PRETTY_FUNCTION__ << endl; }
template <typename T> void byRef(T &t)        { cout << "byRef  " << __PRETTY_FUNCTION__ << endl; }
template <typename T> void byCRef(const T &t) { cout << "byCRef " << __PRETTY_FUNCTION__ << endl; }
template <typename T> void byFwd(T &&t)       { cout << "byFwd  " << __PRETTY_FUNCTION__ << endl; }

int main()
{
    int i = 1;
    const int ci = 2;

    cout << "--- 实参是左值 int ---" << endl;
    byVal(i);
    byRef(i);
    byCRef(i);
    byFwd(i);

    cout << "--- 实参是右值 3 ---" << endl;
    byVal(3);
    byCRef(3);
    byFwd(3);

    cout << "--- 实参是 const 左值 ---" << endl;
    byVal(ci);
    byRef(ci);
    byCRef(ci);
    byFwd(ci);
}
```

实测输出:

```
--- 实参是左值 int ---
byVal  void byVal(T) [with T = int]
byRef  void byRef(T&) [with T = int]
byCRef void byCRef(const T&) [with T = int]
byFwd  void byFwd(T&&) [with T = int&]
--- 实参是右值 3 ---
byVal  void byVal(T) [with T = int]
byCRef void byCRef(const T&) [with T = int]
byFwd  void byFwd(T&&) [with T = int]
--- 实参是 const 左值 ---
byVal  void byVal(T) [with T = int]
byRef  void byRef(T&) [with T = const int]
byCRef void byCRef(const T&) [with T = int]
byFwd  void byFwd(T&&) [with T = const int&]
```

三条读法:

- **按值的 `T` 最干净**:`const int ci` 进来也只是 `int` —— 反正要拷一份,顶层 const 不带走
- **`T &` 会把实参的 const 带进 T**:`byRef(ci)` 推成 `const int`,所以"只读参数"不要写 `T &`
- **只有 `T &&` 会把 T 推成引用类型**:左值进来 `T = int&`,右值进来 `T = int`

### 19.2 引用折叠:引用的引用是谁

规则四条:

| 组合 | 折叠成 |
| --- | --- |
| `T& &` | `T&` |
| `T& &&` | `T&` |
| `T&& &` | `T&` |
| `T&& &&` | `T&&` |

一句话记:**只要有一个是左值引用,结果就是左值引用;两个都是右值引用才是右值引用**(左值引用赢)。

用类型别名制造"引用的引用"来实测(直接手写是编译不过的,见下面坑):

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

using L = int &;    // 给"左值引用类型"起个别名
using R = int &&;   // 给"右值引用类型"起个别名

int main()
{
    int i = 1;

    L &a = i;       // int& &   -> int&
    L &&b = i;      // int& &&  -> int&
    R &c = i;       // int&& &  -> int&
    R &&d = 1;      // int&& && -> int&&

    cout << "L&  折成 int&  : " << is_same<decltype(a), int &>::value << endl;
    cout << "L&& 折成 int&  : " << is_same<decltype(b), int &>::value << endl;
    cout << "R&  折成 int&  : " << is_same<decltype(c), int &>::value << endl;
    cout << "R&& 折成 int&& : " << is_same<decltype(d), int &&>::value << endl;
}
```

实测输出:

```
L&  折成 int&  : 1
L&& 折成 int&  : 1
R&  折成 int&  : 1
R&& 折成 int&& : 1
```

### 19.3 为什么模板里的 T&& 左值右值都能接

以 `byFwd` 为例,折叠发生在"把 T 代进去"的那一刻:

- `byFwd(i)`(左值):T 推成 `int&` → 形参变成 `int& &&` → 折叠成 `int&` → 接得住左值
- `byFwd(3)`(右值):T 推成 `int` → 形参就是 `int &&` → 接得住右值

所以**形参写成 `T&&`、且 T 是模板参数**的这种写法叫转发引用,它左值右值都能接,和普通右值引用不是一回事:

```cpp
void f(int &&x);      // 普通右值引用:只接右值(第 4 节那条)

template <typename T>
void g(T &&t);        // 转发引用:左值右值都接
```

反过来的实证:显式写模板实参就关掉了推导,折叠也不发生 —— `T = int`,形参就是 `int&&`,左值接不了:

```cpp
byFwd<int>(i);   // error: cannot bind rvalue reference of type ‘int&&’ to lvalue of type ‘int’
```

### 19.4 std::forward<T> 为什么必须写 <T>

```cpp
#include <iostream>
#include <utility>
using namespace std;

void sink(int &x)  { cout << "   sink -> 左值版本" << endl; }
void sink(int &&x) { cout << "   sink -> 右值版本" << endl; }

template <typename T> void wrapper(T &&t)
{
    cout << "T = " << (is_lvalue_reference<T>::value ? "int&  (实参是左值)" : "int   (实参是右值)") << endl;

    sink(t);                   // t 是有名字的变量 -> 左值
    sink(std::forward<T>(t));  // 按 T 还原成进来时的那个值类别

    cout << endl;
}

int main()
{
    int i = 1;

    cout << "wrapper(i) 传左值:" << endl;
    wrapper(i);

    cout << "wrapper(2) 传右值:" << endl;
    wrapper(2);
}
```

实测输出:

```
wrapper(i) 传左值:
T = int&  (实参是左值)
   sink -> 左值版本
   sink -> 左值版本

wrapper(2) 传右值:
T = int   (实参是右值)
   sink -> 左值版本
   sink -> 右值版本
```

- 不管实参是左是右,`t` 进到函数体里**都是有名字的变量 = 左值**(第 4 节最后那条),所以 `sink(t)` 永远走左值版本 —— 右值性在"传进去"那一步就丢了
- `std::forward<T>(t)` 干的活就是**把进来时的值类别还回去**:`T` 是 `int&` 就给左值,`T` 是 `int` 就给右值
- `<T>` 不能省:实测写成 `std::forward(t)` 报 `error: no matching function for call to ‘forward(int&)’` —— forward 的模板参数从实参推不出来,它要的就是你显式告诉它 T

那拿 `std::move(t)` 替 `forward<T>(t)` 会怎样(实测):左值进来也会被当成右值送出去 ——

```
wrapper(i) 传的是左值,里面用 move:
   sink -> 右值版本
wrapper(2) 传的是右值:
   sink -> 右值版本
```

区别一句话:**`move` 是无条件"当右值",`forward<T>` 是"按 T 还原"**。完美转发的固定搭配就是 `T&&` 形参 + `std::forward<T>(t)`。

### 19.5 auto&& 是同一条规则

```cpp
#include <iostream>
#include <type_traits>
using namespace std;

int main()
{
    int i = 1;

    auto &&r1 = i;   // 实参是左值:auto = int&  -> int& && -> int&
    auto &&r2 = 2;   // 实参是右值:auto = int   -> int &&   -> int&&

    cout << "auto&& 接左值,是左值引用: " << is_lvalue_reference<decltype(r1)>::value << endl;
    cout << "auto&& 接右值,是右值引用: " << is_rvalue_reference<decltype(r2)>::value << endl;
}
```

实测输出:

```
auto&& 接左值,是左值引用: 1
auto&& 接右值,是右值引用: 1
```

`auto&&` 和模板 `T&&` 走的是同一套推导 + 折叠:`r1` 其实是 `int&`,`r2` 是 `int&&`(这也是 `for (auto &&x : v)` 能改容器元素的原因)。

坑:

- **直接手写"引用的引用"是语法错误**,折叠只发生在 typedef / 模板参数替换的地方。实测 `int & &e = i;` 报:`error: cannot declare reference to ‘int&’, which is not a typedef or a template type argument`
- 模板里的 `T&&` 别当"右值引用"用:它接左值时 `T` 会被推成 `int&`,实参的 const 也一起带进来(`const int ci` 进来是 `const int&`)
- `T&&` 的函数里忘写 `forward<T>`(只写 `sink(t)`):右值实参也走左值版本,白白多一次拷贝;写成 `sink(std::move(t))`:左值实参也被当右值送走,可能把调用者的对象搬空
- `T &` 形参接不了右值,报错原文:`cannot bind non-const lvalue reference of type ‘int&’ to an rvalue of type ‘int’`;只读参数写 `const T&`
- 显式写模板实参(`byFwd<int>(i)`)等于手动指定 T,推导和折叠都不发生 —— 看到"怎么又接不了左值了",先看是不是自己把实参写全了
- `std::forward` 不带模板实参根本编不过,别指望编译器替你推

## 20. 函数模板在什么时候“生成”函数
> 记录于:2026-09-17

一句话:函数模板自己不是函数,只是一张“模板”;编译器在**编译**用到它的那个 `.cpp` 时,按实参类型就地生成一个普通函数(这个过程叫**实例化**),不是运行时,运行时那边也一点开销都没有。

| 阶段 | 模板发生的事 |
| --- | --- |
| 编译某个 `.cpp` | 按实参类型实例化出具体函数,写进这个 TU 的 `.o`;名字被 mangle 成 `_Z3addIiET_S0_S0_` 这种 |
| 链接 | 各 `.o` 里生成的那几份同名实例是 `WEAK`/COMDAT 符号,链接器合并成一份 |
| 运行 | 什么都不发生:调用的就是普通函数,和手写重载一样 |

### 20.1 生成发生在编译期:`.o` 里已经是机器码

```cpp
#include <cstdio>

template <class T>
T add(T a, T b) { return a + b; }

int main()
{
    printf("%d\n", add(1, 2));
    add(1.5, 2.5);
    add('a', 'b');
}
```

只编译、不链接,看 `t.o` 的符号表:

```
g++ -c t.cpp -o t.o
nm -C --defined-only t.o | grep 'add<'
```

实测输出:

```
0000000000000000 W char add<char>(char, char)
0000000000000000 W double add<double>(double, double)
0000000000000000 W int add<int>(int, int)
```

三种实参类型 = 三份代码,`W` 表示 weak 符号(下面 20.2 用得上)。再看 `add<int>` 那份的真身:

```
objdump -d --demangle t.o | sed -n '/add<int>/,/ret/p'
```

实测输出:

```
0000000000000000 <int add<int>(int, int)>:
   0:	f3 0f 1e fa         	endbr64
   4:	55                  	push   %rbp
   5:	48 89 e5            	mov    %rsp,%rbp
   8:	89 7d fc            	mov    %edi,-0x4(%rbp)
   b:	89 75 f8            	mov    %esi,-0x8(%rbp)
   e:	8b 55 fc            	mov    -0x4(%rbp),%edx
  11:	8b 45 f8            	mov    -0x8(%rbp),%eax
  14:	01 d0               	add    %edx,%eax
  16:	5d                  	pop    %rbp
  17:	c3                  	ret
```

这就是普通函数:参数入栈、`add` 一下、`ret`。所以 `-c` 出来的 `.o` 里机器码已经齐了,链接期和运行期都插不上手。

### 20.2 一个类型一份,多份靠 weak 符号合并(模板为什么要写 `.h`)

`hdr.h` 里写模板定义,`u1.cpp` 和 `u2.cpp` 各自 `#include` 它、各调一次 `add(1, 2)`:

```cpp
// hdr.h
#pragma once

template <class T>
T add(T a, T b) { return a + b; }   // 定义就写在头文件里
```

```cpp
// u1.cpp(实际是 #include "hdr.h",这里把展开后的定义写出来);u2.cpp 一模一样,只是函数名换成 f2
template <class T>
T add(T a, T b) { return a + b; }

int f1() { return add(1, 2); }
```

两个 `.o` **各自都生成了** `add<int>`:

```
g++ -c u1.cpp -o u1.o ; g++ -c u2.cpp -o u2.o
nm -C --defined-only u1.o | grep 'add<'
nm -C --defined-only u2.o | grep 'add<'
```

实测输出:

```
0000000000000000 W int add<int>(int, int)
0000000000000000 W int add<int>(int, int)
```

两份同名符号没打架,靠的是 `WEAK` 和它所在的那节 COMDAT:

```
readelf -sW u1.o | grep _Z3add
```

实测输出:

```
3: 0000000000000000     0 SECTION LOCAL  DEFAULT    6 .text._Z3addIiET_S0_S0_
5: 0000000000000000    24 FUNC    WEAK   DEFAULT    6 _Z3addIiET_S0_S0_
```

两份合起来只剩一份:

```
g++ u1.o u2.o -shared -o libu.so
nm -C --defined-only libu.so | grep 'add<'
```

实测输出:

```
0000000000001132 W int add<int>(int, int)
```

要点:不是“编译器只生成一次”,而是**每个用到它的 TU 各生成一份**,靠 `WEAK` 符号在链接期合并成一份。反过来说,模板的定义必须对“使用它的那个 TU”可见,这就是模板一律写进头文件的原因。

### 20.3 反例:头文件只留声明 → 编译能过,链接才炸

```cpp
// a.cpp(头文件里只写了声明的展开版)
template <class T>
T add2(T a, T b);          // 只有声明

int main() { return add2(1, 2); }
```

```cpp
// def.cpp:定义在这儿,但这个文件自己不调用 add2
template <class T>
T add2(T a, T b) { return a + b; }
```

`a.cpp` 编译一帆风顺,`.o` 里只欠着一条引用:

```
nm -C a.o | grep add2
```

实测输出:

```
                 U int add2<int>(int, int)
```

`def.cpp` 编出来的 `.o` 里**什么都没有** —— 定义了模板但没人用,就不生成。于是链接阶段:

```
g++ a.o def.o -o a.bin
```

实测输出:

```
/usr/bin/x86_64-linux-gnu-ld.bfd: a.o: in function `main':
a.cpp:(.text+0x13): undefined reference to `int add2<int>(int, int)'
collect2: error: ld returned 1 exit status
```

`U` = 未定义引用(undefined)。将来看到**带模板实参**的 `undefined reference to ‘int f<int>(int)’`,先查“定义在哪、那个 TU 看得见吗”,别去怀疑运行时。

### 20.4 谁触发实例化:隐式、显式、`extern template`

1. **隐式实例化**(日常默认):用到了就生成,类型从调用现场推。

```cpp
#include <cstdio>

template <class T>
T add(T a, T b) { return a + b; }

int main()
{
    printf("%d\n", add(1, 2));      // 生成 add<int>
    printf("%f\n", add(1.5, 2.5));  // 生成 add<double>
}
```

2. **显式实例化定义**(`template` 开头、后面给全类型):没人调用也生成,可以把“生成”集中放一个 `.cpp` 里。

```cpp
template <class T>
T add(T a, T b) { return a + b; }

template double add<double>(double, double);   // 显式实例化:没人调用也生成
template int add<int>(int, int);
```

```
g++ -c e.cpp -o e.o ; nm -C --defined-only e.o | grep 'add<'
```

实测输出(两个都没被调用,照样有):

```
0000000000000000 W double add<double>(double, double)
0000000000000000 W int add<int>(int, int)
```

3. **实例化声明 `extern template`**(C++11):告诉编译器“这份实例在别处生成,本 TU 别生成”,只留一条欠着的引用 —— 这是压编译时间的手段。

```cpp
template <class T>
T add(T a, T b) { return a + b; }

extern template int add<int>(int, int);        // 告诉编译器:这份实例在别处生成

int g() { return add(1, 2); }
```

```
g++ -c x.cpp -o x.o ; nm -C x.o | grep 'add<'
```

实测输出:

```
                 U int add<int>(int, int)
```

### 20.5 编译期“求值”和编译期“生成函数”不是一回事

实例化生成的是**函数体**;`constexpr` / `consteval` 生成的是**一个值**。两者都发生在编译期,但用途不同:模板解决“写一遍对付各种类型”,`constexpr` 解决“把计算提前到编译期”。

```cpp
#include <iostream>
using namespace std;

// 编译期"求值":算出来的是值,不是函数体
constexpr int factorial(int n) { return n <= 1 ? 1 : n * factorial(n - 1); }

// 模板元编程:靠模板特化递归算值
template <int N>
struct Fact { static constexpr int value = N * Fact<N - 1>::value; };
template <>
struct Fact<0> { static constexpr int value = 1; };

int main()
{
    constexpr int a = factorial(5);   // 编译期就变成 120
    cout << a << " " << Fact<5>::value << endl;
}
```

实测输出:

```
120 120
```

C++20 的 `consteval` 是更硬的版本:必须在编译期算,运行期调不过编译(要 `-std=c++20`):

```cpp
#include <iostream>
using namespace std;

consteval int square(int n) { return n * n; }

int main()
{
    constexpr int y = square(4);   // 编译期
    cout << y << endl;
    // int z = square(rand());     // 错误:consteval 不许在运行期调用
}
```

实测输出:

```
16
```

### 20.6 `static_assert` 只有被实例化时才检查

模板没被实例化之前,里面的 `static_assert` 看着人畜无害;一实例化就炸,报错还带一整条调用链,看着像运行时,其实全是编译期:

```cpp
#include <type_traits>

template <class T>
int only_int(T)
{
    static_assert(std::is_integral<T>::value, "T 必须是整数");
    return 0;
}

int main()
{
    only_int(1);
    only_int(1.0);   // 加了这句才会炸
}
```

实测报错:

```
s.cpp: In instantiation of ‘int only_int(T) [with T = double]’:
s.cpp:13:13:   required from here
s.cpp:6:40: error: static assertion failed: T 必须是整数
s.cpp:6:40: note: ‘std::integral_constant<bool, false>::value’ evaluates to false
```

坑:

- **模板定义写进 `.cpp` 就等着 `undefined reference`**:定义必须对“使用它的那个 TU”可见;真要放 `.cpp`,就在那儿补一行显式实例化 `template int add<int>(int, int);` 把代码生成出来(见 20.3、20.4)
- **别把“实例化”当成运行时的东西**:报错行里的 `In instantiation of ...` / `required from here` 是编译器在告诉你“我从这个调用点往里展开模板时出错了”,不是运行期信息
- **两阶段查找**:模板定义时先查跟 `T` 无关的名字,跟 `T` 有关的名字推迟到实例化那一刻才查(靠实参类型做 ADL)。所以“定义时看着没毛病、一实例化就说找不到函数”属于正常现象
- **代码膨胀**:每个类型组合一份独立代码,`add<int>` 和 `add<long>` 是两份,`vector<A>` 和 `vector<B>` 也是两份;类型多、模板嵌套深时二进制会涨,这也是模板让编译变慢的主要原因
- **运行时零开销**:模板不引入任何运行期机制 —— 没有类型信息、没有查表、没有动态生成代码,和手写重载一样;真正“运行时代码生成”的只有 JIT 那类库(如 Cling、LLVM ORC),跟标准 C++ 模板不是一回事
- **`WEAK` 只保模板这类多处生成的实体**:两个 `.cpp` 都实例化 `add<int>` 不会撞车(20.2);但**非模板**的函数/变量写进头文件而不加 `inline`(或 `static`),是真的会 `multiple definition`

## 21. 成员函数末尾的 const
> 记录于:2026-09-17

一句话:成员函数末尾那个 `const` 修饰的是隐藏的 `this`,含义是“我不改自己”;参数上的 `const` 管的是对方。两者互不替代 —— 参数写 `const T&` 只解决“别人能不能传进来”,末尾 `const` 才解决“我能不能被 const 对象调用”。

### 21.1 末尾 const 修饰谁

```cpp
class Cat {
    int age_;
public:
    int operator*(const Cat &other) const;   // 末尾 const:承诺不改自己
};
```

展开看,末尾的 const 就是把隐藏的 this 声明成 `const Cat *`(相当于 `int Cat::operator*(const Cat *this, const Cat &other);`,真写出来是语法错误,这行只是帮助理解):

所以它决定的是**谁有资格调用这个成员函数**:

| 调用方 | 非 const 成员函数 | const 成员函数 |
| --- | --- | --- |
| `Cat c;` 普通对象 | ✓ | ✓ |
| 临时对象 `Cat("Tom", 18)` | ✓ | ✓ |
| `const Cat cc;` | ✗ | ✓ |
| `const Cat &r` / `const Cat *p` | ✗ | ✓ |

同一个类里一个函数写两版,就能看出差别:

```cpp
#include <iostream>
#include <string>
using namespace std;

class Cat {
    string name_;
    int age_;
public:
    Cat(string name, int age) : name_(std::move(name)), age_(age) {}
    int  getAgeNC()       { return age_; }   // 非 const 版本
    int  getAge() const   { return age_; }   // const 版本
};

int main() {
    Cat c("Jack", 5);
    const Cat cc("Rose", 2);

    cout << "非const对象  -> 非const: " << c.getAgeNC() << " | const: " << c.getAge() << endl;
    cout << "const对象    -> const: " << cc.getAge() << endl;
    // cc.getAgeNC();        // 这一行会报错
    cout << "临时对象     -> 非const: " << Cat("Tom", 18).getAgeNC()
         << " | const: " << Cat("Ann", 7).getAge() << endl;
}
```

实测输出:

```
非const对象  -> 非const: 5 | const: 5
const对象    -> const: 2
临时对象     -> 非const: 18 | const: 7
```

把 `cc.getAgeNC();` 的注释去掉,立刻报:

```
error: passing ‘const Cat’ as ‘this’ argument discards qualifiers [-fpermissive]
note:   in call to ‘int Cat::getAgeNC()’
```

翻译:要把一个 `const Cat` 当 `this` 传进去,这会丢掉 const,所以编译器拒绝。

### 21.2 为什么参数加了 const,末尾也必须加 —— 四种组合

`a1 * a2` 会被当成 `a1.operator*(a2)`,a1 就是这次调用的 `this`。参数写成 `const T&` 之后,a1 是 const 左值,它的 `this` 就是 `const Cat*`,只有末尾带 const 的成员函数接得住:

| 成员函数写法 | 用 `fun(const T&, const T&)` 调 | 结果 |
| --- | --- | --- |
| `int operator*(Cat &other)` | 参数、this 都不够 | ✗ `passing ‘const Cat’ as ‘this’ argument discards qualifiers` |
| `int operator*(const Cat &other)` | 参数够了,this 不够 | ✗ 同一个错 |
| `int operator*(Cat &other) const` | this 够了,参数不够 | ✗ `binding reference of type ‘Cat&’ to ‘const Cat’ discards qualifiers` |
| `int operator*(const Cat &other) const` | 两个都够 | ✓ |

两种都写全的完整程序:

```cpp
#include <iostream>
#include <string>
using namespace std;

template <class T>
int fun(const T &a1, const T &a2) { return a1 * a2; }

class Cat {
    string name_;
    int age_;
public:
    Cat(string name, int age) : name_(std::move(name)), age_(age) {}
    int operator*(const Cat &other) const { return age_ * other.age_; }
};

int main() {
    Cat c1("Jack", 5), c2("Rose", 2);
    cout << fun(c1, c2) << endl;
}
```

实测输出:

```
10
```

看第二行和第三行的错法不一样:参数上的 const 和末尾的 const 各自管一头,谁缺了都在自己那头报错。**参数 const 让对方能收 const 对象,末尾 const 让自己能被 const 上下文调用。**

### 21.3 那按值传参为什么不报错

因为按值传进函数体的形参是**非 const 的副本**(第 19 节那条:按值传参会剥掉顶层 const),副本能调非 const 成员函数:

```cpp
template <class T>
int fun(T a1, T a2) { return a1 * a2; }   // 形参按值, 副本不带 const
```

同样的类、同样的 `operator*(Cat &other)`,这个版本能编过,输出还是 `10`。

所以没写末尾 const 不算“写错”,算“挑食”:只要下面任一条成立,它就立刻编不过 ——

- `fun` 的形参改成 `const T&`(省一次拷贝,推荐写法)
- 拿 `const Cat cc(...)`、`const Cat &r`、`const Cat *p` 来调
- 对象是容器里的 const 元素(`for (const auto &c : v)` 里的 `c`)
- 被某个 const 成员函数里的代码使用

### 21.4 该加的、不该加的

- **加**:getter、`>` `<` `==` `!=`、`*` `+` `-` 这类比较/算术运算符 —— 它们只看不改,加了才“哪里都能用”
- **不加**:`+=` `-=`、`setAge`、`swap`、`operator=`(赋值运算符必须改自己,加了 const 编不过)
- 两种都要就写 const 重载,编译器按 this 的 const 性挑
- 例外:标了 `mutable` 的成员,在 const 成员函数里也能改(缓存、计数这类“逻辑上不算改状态”的东西)

坑:

- **三个 const 位置别混**:返回值 `const Cat operator+()`、参数 `const Cat &other`、末尾 `operator+() const` —— 管的对象各不相同,末尾那个只管 `this`
- **末尾 const 的函数里,`this` 是 `const Cat*`**:不能改成员(除非 `mutable`),也不能调用同类的非 const 成员函数
- **两种报错对号入座**:`passing ‘const X’ as ‘this’ argument discards qualifiers` = 成员函数少了末尾 const;`binding reference of type ‘X&’ to ‘const X’ discards qualifiers` = 参数少了 const
- **末尾 const 只挡自己不改,挡不住“漏出去”**:const 成员函数返回成员的引用/指针,外面照样能借它改内部;要么返回 const 引用,要么返回副本
- **想自检就换成 const 对象调一遍**:自己在 const 上下文里的行为,只有 `const Cat cc;` 或 `const Cat &r = c;` 才试得出来
