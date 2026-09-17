# C++ 标准库函数/类速查

> 创建时间:2026-09-17

收录已经学过的 std 里的函数和类,按"在哪个头文件里"分节。只写用法和坑,机制讲解看 cpp-note.md。

## 目录

- [C++ 标准库函数/类速查](#c-标准库函数类速查)
  - [目录](#目录)
  - [1. std::string 成员函数](#1-stdstring-成员函数)
  - [2. 数字和字符串互转](#2-数字和字符串互转)
  - [3. cout cin 和 getline](#3-cout-cin-和-getline)
  - [4. std::ostringstream](#4-stdostringstream)
  - [5. std::istringstream](#5-stdistringstream)
  - [6. 格式控制 iomanip](#6-格式控制-iomanip)
  - [7. std::format](#7-stdformat)
  - [8. 标准异常类](#8-标准异常类)
  - [9. std::move 和 std::forward](#9-stdmove-和-stdforward)
  - [10. type\_traits 类型判断](#10-type_traits-类型判断)
  - [11. 容器 array vector list](#11-容器-array-vector-list)
  - [12. 常用的 C 库函数](#12-常用的-c-库函数)

## 1. std::string 成员函数
> 记录于:2026-09-17

头文件 `<string>`。高频就三类:问大小、拿一段、改它自己。带"改"的(insert / erase / replace / += / clear)一律原地生效;find / substr / 比较只读。

| 函数 | 作用 |
| --- | --- |
| `size()` / `length()` | 元素个数(返回 `size_t`) |
| `empty()` | 是否为空 |
| `s[i]` / `s.at(i)` | 取字符:`[]` 不检查越界,`at` 越界抛 `out_of_range` |
| `front()` / `back()` | 首字符 / 尾字符 |
| `s += "a"` / `append()` / `push_back('c')` | 加串 / 加串 / 加单个字符 |
| `insert(pos, s)` | 在下标 pos 处插入 |
| `erase(pos, n)` | 从 pos 起删 n 个 |
| `replace(pos, n, s)` | 从 pos 起 n 个字符换成 s |
| `pop_back()` / `clear()` | 删末尾一个 / 清空 |
| `find(s)` / `find(s, pos)` / `rfind(s)` | 首次出现 / 从 pos 往后找 / 最后一次出现 |
| `string::npos` | find 找不到时的返回值(不是 -1) |
| `substr(pos, n)` | 截取一段(起点 + 个数),不动原串 |
| `==` `<` `>` / `compare()` | 整串比较(别用 strcmp) |
| `c_str()` / `data()` | 取出 `const char*` 交给 C API |
| `reserve(n)` / `resize(n)` / `capacity()` | 提前要容量 / 改长度(变长补 `'\0'`) / 看容量 |
| `begin()` / `end()` | 迭代器,能被范围 for 和算法用 |

坑:

- `size()` 是无符号,倒序遍历别写 `for (int i = s.size() - 1; i >= 0; i--)`,写 `for (size_t i = s.size(); i-- > 0; )`
- `find` 失败给 `string::npos`,判断只能写 `== string::npos`
- `c_str()` 的指针是借来的:string 一改(可能重新分配)、对象一销毁就作废;想留着先 `strcpy` 一份
- `erase` / `replace` / `substr` 参数都是"起点 + 个数",没有给终点下标的写法

## 2. 数字和字符串互转
> 记录于:2026-09-17

头文件 `<string>`。

```cpp
stoi("42") + 1          // 43  字符串 -> int
stod("3.5") * 2         // 7   字符串 -> double
to_string(7) + "!"      // 7!  int    -> 字符串
```

坑:

- `stoi` 不查尾巴:`stoi("12abc")` = 12,不报错;一个数字都没有抛 `invalid_argument`,超范围抛 `out_of_range`
- `to_string` 打浮点固定 6 位小数,想要别的精度得用 ostringstream / std::format

实测输出:

```
to_string(3.5)=3.500000  to_string(3.14159265)=3.141593  to_string(0.0001)=0.000100
```

## 3. cout cin 和 getline
> 记录于:2026-09-17

头文件 `<iostream>` 和 `<string>`。

```cpp
cout << "x=" << x << endl;
cin >> s;                    // 遇空格/Tab/换行就停
cin.ignore(1000, '\n');      // 吃掉这一行剩下的内容
getline(cin, s);             // 读一整行,空格保留,换行丢掉
```

#### endl 和 "\n" 的区别

`"\n"` 只是往流里塞一个换行字符;`endl` 是"塞换行 + 强制刷新缓冲区"。libstdc++ 里的实现就一行(`/usr/include/c++/15/ostream:64-67`):

```cpp
template<typename _CharT, typename _Traits>
inline basic_ostream<_CharT, _Traits>&
endl(basic_ostream<_CharT, _Traits>& __os)
{ return flush(__os.put(__os.widen('\n'))); }     // 写 '\n' 之后多调了一次 flush
```

flush = 把缓冲区里攒着的字节真正 write() 给操作系统。平时这些字节是攒着的,攒够或者程序正常结束才一次写出去。

实测(自定义 streambuf 数 flush 次数,打 5 行):

```
用 endl  : flush(sync) 被调用 5 次
用 "\n"  : flush(sync) 被调用 0 次
```

100 万行输出重定向到文件:

```
endl : real 1.214s(sys 0.998s)      两个输出文件字节数相同(11888890),diff 一致
"\n" : real 0.071s(sys 0.004s)      ← 快 17 倍,内容一点没少
```

什么时候才需要 endl / flush:

- 程序可能非正常退出(abort、段错误、没接住的异常)之前要留日志 —— 这时缓冲区里的东西会丢(cpp-note 第 9 节那个 double free,cout 的内容一条都没打出来就是这个原因)
- fork 之前:父子进程各持一份缓冲区,会重复输出
- 写日志文件要立刻落盘、长时间运行的程序想实时看到进度
- 交互式提问的提示语(其实不用手动刷:cin 和 cout 是 tie 的,读 cin 之前会自动刷 cout)

其他一律用 `"\n"`。写 `'\n'`(单引号)比 `"\n"` 更精确:前者是 char,后者是字符串字面量,要多走一遍长度计算。近亲还有 `flush`(只刷不换行)和 `ends`(插一个 `'\0'` 再刷)。

内存流是例外:`ostringstream` 没有底层设备可刷,对它调 flush 是空操作 —— 第 4 节例子里那个 `os << ... << endl` 的 endl 是白写。

坑:

- `cin >>` 后面接 `getline`:缓冲区里剩的换行要先 `cin.ignore(1000, '\n')` 吃掉,否则 getline 读到空行
- `cout << nullptr`(比如空的 `char *` 成员)不崩,但会把 cout 置成失败状态,之后所有输出静默消失 —— 打印指针成员前先判空

## 4. std::ostringstream
> 记录于:2026-09-17

头文件 `<sstream>`。内存里的一个"输出流",用法和 cout 完全一样,只是写进去的东西不进屏幕,最后 `.str()` 取成 `std::string`。

```cpp
#include <sstream>

std::ostringstream os;
os << "id=" << id << " name=" << name;   // 和 cout 一样链式 <<
std::string s = os.str();                // 取内容
```

实测输出:

```
id=7 v=3.5 name=kitty
```

要点和坑(全部实测):

- **数字格式化靠 `<iomanip>`**(见第 6 节),不是 printf 的 `%d`;`os << fixed << setprecision(2) << 3.14159` 得 `3.14`
- **清空内容用 `os.str("")`;`os.clear()` 清的是状态标志(failbit 之类),不是内容**。实测 clear() 之后 `os.str()` 还是 `abc`。要复用得写两句:`os.str(""); os.clear();`
- `os.str("x")` 是"把内容换成 x 并把写位置挪回开头",之后再 `<<` 是**覆盖**不是追加 —— 实测 `str("x")` 后 `<< "y"` 得到 `y`,不是 `xy`
- 只能写不能读:`os >> x` 编译不过(`no match for 'operator>>'`);读用 `istringstream`(第 5 节),一读一写用 `stringstream`
- 自定义类型要自己写 `operator<<`,否则报 `no match for 'operator<<'` 加几十行候选(和 cout 同源)
- 左值 `os << "a"` 返回的是 `basic_ostream&`,所以 `(os << "a").str()` 编译不过;临时对象 `(std::ostringstream() << "a").str()` 反而能编译(libstdc++ 给右值流加了专用重载)。知道有这回事就行,别写进代码
- 不用为性能提前绕路:实测 20 万次拼接,ostringstream 13ms vs `s += "num=" + to_string(i)` 10ms,同一量级;循环里每次新建对象 vs `str("")` + `clear()` 复用,21ms vs 23ms —— **复用并不更快**

什么时候用它:要和 printf 一样做对齐、补零、十六进制、控制小数位时最顺手;单纯拼几个字符串用 `s += to_string(n)` 更短;能上 C++20 就优先 `std::format`(第 7 节)。

## 5. std::istringstream
> 记录于:2026-09-17

头文件 `<sstream>`。把一个字符串当成输入流,用 `>>` 按类型往里拆,用法和 cin 一样。

```cpp
#include <sstream>

std::istringstream in("12 3.5 abc");
int a; double b; std::string c;
in >> a >> b >> c;                    // 12 3.5 abc,空格自动跳过

std::istringstream in2("Jack,100,90");
std::string name, s1, s2;
getline(in2, name, ',');              // 指定分隔符读一段
getline(in2, s1, ',');
getline(in2, s2);                     // 不写分隔符就是读到行尾
```

实测输出:

```
拆出: 12 3.500000e+00 abc  流到末尾了吗(fail): 0
按逗号拆: Jack|100|90
```

坑:

- **失败之后流会一直坏着**:`in >> x` 读到不是数字的内容时 `fail()` 变 1,`x` 没被赋值(是垃圾值);此后所有读取全部失败。要接着用必须先 `in.clear()` 清状态,再 `in.str("新内容")` 换内容 —— 实测 `clear()` + `str("42")` 之后才读得出来
- `>>` 读 `std::string` 遇空格就停,想读带空格的一整段用 `getline`
- 读数字时 `>>` 会自动跳过前面的空格和换行,所以 `getline` 和 `>>` 混用要注意剩下什么

## 6. 格式控制 iomanip
> 记录于:2026-09-17

头文件 `<iomanip>`(数字进制在 `<ios>` 里)。都是"往流里塞一个操纵器",对 cout 和 ostringstream 通用。

| 操纵器 | 作用 |
| --- | --- |
| `setprecision(n)` | 精度:默认模式下是**有效数字**位数,配 `fixed` 后才是小数位数 |
| `fixed` / `scientific` | 定点(小数形式) / 科学计数法 |
| `setw(n)` | 下一个输出项的最小宽度(补空格) |
| `setfill('c')` | 补位字符,默认空格 |
| `left` / `right` | 左对齐 / 右对齐 |
| `hex` / `oct` / `dec` | 十六进制 / 八进制 / 十进制 |
| `showbase` | 十六进制带 `0x` 前缀 |
| `boolalpha` / `noboolalpha` | 布尔打印成 `true/false` / 打印成 `1/0` |

```cpp
std::ostringstream f;
f << hex << 255 << " " << oct << 8 << " " << dec << 10 << " | "
  << setw(6) << setfill('*') << right << 42 << " | "
  << left << setw(4) << "ab" << "|";
```

实测输出:

```
ff 10 10 | ****42 | ab**|
```

坑:

- **`setw` 只作用下一个输出项,别的都是"粘"的**:实测 `setw(6) << 42` 得 `****42`,紧接着 `setw(6) << 7` 还会补空格;而 `setfill('*')` 会一直留着,`left` 也会一直留着
- **`fixed` / `hex` 这些标志会在流里一直留着**,对 cout 用就等于污染了后面所有输出:

```
A) 默认(没动过 cout): 3.14159 | 0.0001 | 1234.56
B) 只 setprecision(2) 不 fixed: 1.2e+03
C) 接着打别的数,精度还是 2: 1e+02
D) fixed 之后: 1234.56  3.14
E) 忘了还原,后面全变小数形式: 0.00
F) hex 是粘的: ff ff 255
```

  B 那行说明:`setprecision(2)` 单独用是"有效数字 2 位",1234.56 被压成 `1.2e+03`;要小数位数必须配 `fixed`。所以这些操纵器尽量只用在临时流(ostringstream)上,别对全局 cout 下手。

## 7. std::format
> 记录于:2026-09-17

头文件 `<format>`,C++20 起。这是 C 里 `snprintf` 在 C++ 的对应物:直接返回 `std::string`,不用给缓冲区。

```cpp
#include <format>

std::string s = std::format("id={} v={:.1f} name={}", id, v, name);   // 占位符是 {},不是 %d/%s
std::format("{:>6}|{:#x}", 42, 255)                                   // 对齐、十六进制
```

编译要加 `-std=c++20`(实测 g++ 15.2.0 可用)。实测输出:

```
id=7 v=3.5 name=kitty
    42|0xff
```

和 snprintf 的三条实质区别(都实测过):

- **不用给缓冲区,也没有截断问题**:snprintf 要 `char buf[N]` + `sizeof`,写小了静默截断;format 返回长度自适应的 `std::string`
- **格式串必须是编译期常量**:把格式串放进 `std::string f` 再传给 `std::format(f, x)` 直接编译不过,报 `call to consteval function ... is not a constant expression`
- **类型对不上编译期就拦**:实测 `std::format("{:d}", 3.5)` 编译报错;同类的错在 C 里只是警告 —— `snprintf(b, sizeof b, "%d", "abc")` 用 `-Wall` 也只给 warning,程序照样跑,打出来是垃圾值

配套的异常类是 `std::format_error`。编译器太老不支持 `<format>` 时,用第三方 `fmt::format`(std::format 的原型,写法完全一样)。

## 8. 标准异常类
> 记录于:2026-09-17

头文件 `<stdexcept>`(基类 `std::exception` 在 `<exception>`,`bad_cast` 在 `<typeinfo>`)。

| 异常类 | 什么时候抛 |
| --- | --- |
| `std::exception` | 所有标准异常的基类,只有 `what()` |
| `std::runtime_error` | 运行期逻辑错,自己 throw 时最常用 |
| `std::out_of_range` | 越界:`s.at()`、`stoi` 超范围 |
| `std::invalid_argument` | 参数不合法:`stoi("abc")` |
| `std::bad_alloc` | `new` 失败 |
| `std::bad_cast` | `dynamic_cast` 转引用失败 |
| `std::format_error` | `std::format` 格式化出错 |

用得上的几个成员:所有异常都有 `what()` 拿错误消息;`catch` 要**从具体到笼统**,`catch (const exception &e)` 写在前面后面的具体 catch 就永远是死代码;`catch (...)` 兜底但拿不到信息。

自定义异常最省事的写法是继承标准异常:

```cpp
struct MyError : std::runtime_error
{
    int code;
    MyError(int c) : std::runtime_error("我的错误"), code(c) {}   // 先把消息交给基类
};
```

坑:析构函数里不能抛异常(C++11 起析构默认 `noexcept`,抛了直接 `terminate`);throw 的类型和 catch 对不上就没人接,也是 `terminate`。

## 9. std::move 和 std::forward
> 记录于:2026-09-17

头文件 `<utility>`。

```cpp
std::string c = std::move(a);        // 把左值 a 当右值送出去,让 c 走移动构造
sink(std::forward<T>(t));            // 模板里按 T 还原进来时的值类别
```

| 函数 | 一句话 |
| --- | --- |
| `std::move(x)` | 不移动任何东西,只做 `static_cast<T&&>`,无条件"当右值" |
| `std::forward<T>(x)` | 按模板参数 T 把值类别还回去,完美转发的固定搭配是 `T&&` 形参 + `forward<T>(t)` |

坑:

- **`<T>` 不能省**:`std::forward(t)` 报 `no matching function for call to 'forward(int&)'`,它推不出来
- 对 `const` 对象 move 等于白写(`const T&&` 绑不进 `T&&`,还是走拷贝)
- 别写 `return std::move(局部变量);`,会挡住 NRVO —— 实测 `return s;` 零拷贝零移动,`return std::move(s);` 反而多一次移动
- move 之后源对象是"有效但未指定",别再读它
- 左值进来用 `std::move` 送出去会把调用者的对象搬空;该用 `forward<T>` 的地方别用 move

## 10. type_traits 类型判断
> 记录于:2026-09-17

头文件 `<type_traits>`。编译期回答"这个类型是什么",常在 `static_assert`、`if constexpr` 和模板里用。

| 工具 | 作用 |
| --- | --- |
| `is_same<A, B>::value` / `is_same_v<A, B>` | 两个类型是不是同一个 |
| `is_integral<T>::value` / `is_integral_v<T>` | 是不是整数类型(注意 `bool`、`char` 也算) |
| `is_lvalue_reference<T>::value` | 是不是左值引用 |
| `is_rvalue_reference<T>::value` | 是不是右值引用 |
| `remove_reference_t<T>` | 去掉引用,`remove_reference_t<int&&>` 就是 `int` |
| `decltype(表达式)` | 拿到表达式的类型(不是值) |

实测(第 9 节那套推导的实证):

```cpp
auto &&r1 = i;   // 左值:auto = int&  -> int&
auto &&r2 = 2;   // 右值:auto = int   -> int&&
std::is_lvalue_reference<decltype(r1)>::value   // 1
std::is_rvalue_reference<decltype(r2)>::value   // 1
```

调试小技巧:`__PRETTY_FUNCTION__` 打出来的是带完整模板实参的函数名,比如 `void byRef(T&) [with T = const int]`,看清 T 推成了什么。

## 11. 容器 array vector list
> 记录于:2026-09-17

头文件分别是 `<array>`、`<vector>`、`<list>`。

```cpp
std::array<int, 8> a{};          // 定长数组,大小写进模板参数
std::vector<int> v;              // 变长数组
std::list<int> l;                // 双向链表

v.push_back(1);                  // 尾部加一个
v.size();  v[1];  v.front();  v.back();
for (int x : v) cout << x;       // 只读遍历
for (auto &x : v) x *= 10;       // 引用遍历能改元素
```

实测输出:

```
vector size=3 v[1]=2 front=1 back=3 1 2 3
遍历改后: 10 30  加一个: 40
```

要点:

- `array` 是定长(大小是模板参数,也是编译期常量);`vector` 变长、按需扩容;`list` 插入删除便宜但**不能随机访问**(没有 `[]`)
- `v[i]` 不检查越界,`v.at(i)` 检查(抛 `out_of_range`),和 string 一套规矩
- 遍历容器用范围 for:`for (const auto &x : v)` 只读省拷贝,`for (auto &x : v)` 要改元素

## 12. 常用的 C 库函数
> 记录于:2026-09-17

C 的函数在 C++ 里照样能用,只是字符串要拿 `c_str()` 递过去、自己管缓冲区。

| 函数 | 头文件 | 注意 |
| --- | --- | --- |
| `printf` / `snprintf` | `<cstdio>` | 输出目标传 `s.c_str()`;snprintf 要 `char buf[N]` + `sizeof`,写小了静默截断 |
| `strlen` | `<cstring>` | 配 `new char[strlen(s) + 1]`,少写 1 个字节就是堆越界 |
| `strcpy` | `<cstring>` | 连结尾的 `'\0'` 一起写 |
| `strcmp` | `<cstring>` | 在 C++ 里直接比 `std::string` 就行,别用它 |

能上 `std::string` 和 `std::format` 就别用这一节的东西;只有在跟 C 的 API、文件、网络打交道时才需要它们。
