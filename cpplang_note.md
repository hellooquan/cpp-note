# c++易错知识点

## 1. 加入了string这种数据类型

## 2. const_cast,static_cast,dynamic_cast
- const_cast : 专用于去除指针或引用的 const 属性
- static_cast : 与旧式转换相近，但提供了更易于查找的语法，并能有效识别不兼容类型。
- dynamic_cast : 专用于类类型的上下代际间的转换
  
### 2.1 const_cast
  - const_cast 旨在去除标识符的cv限定属性（即 const 与 volatile）
  - const_cast 只能作用于指针或引用类型

```cpp
int main()
{
        int   i = 6; // 普通整型变量
    const int &ri = i; // const型引用，不可修改

    // ×: 以下语句错误
    ri = 8;

    // √: 去除 const 特性后，可以赋值
    const_cast<int &>(ri) = 8;
}
```

处理常目标指针
```cpp
int main()
{
          int  i = 6;
	const int *p = &i; // 常目标指针p，不可修改目标

    // ×: 试图修改常指针的目标，错误！
    *p = 8;

	// √: 去除 const 特性后，可以修改其目标
	*(const_cast<int *>(p)) = 8;



    // ×: 试图扩大权限，错误！
    int *k = p;
        
	// √: 去除 const 特性后，可赋值给普通指针 k 
	int *k = const_cast<int *>(p);
}
```
注意，虽然 const_cast 可以将常目标指针的 const 属性剔除掉，但它不能剔除普通变量和常指针本身的 const 属性。例如：
```cpp
int main()
{
    int        i = 6;  // 普通整型变量
	int *const p = &i; // 常指针

	int j = 8;

    // ×: p是常指针，无法修改其指向
    p = &j;

	// ×: const_cast 不能去除常指针的 const 属性
	const_cast<int *>(p) = &j;
}
```
### 2.2 static_cast
static 意味着静态转换，静态的含义是操作的过程只发生在编译阶段，而不是运行阶段，静态转换不涉及类型推理。

增加可读性
```cpp
int main()
{
	float f = 3.14;

	// 旧式类型转换
	int i = (int)f;
	int i = int(f);
	
	// 新式静态转换，等价于旧式转换:
	// 但是，新式静态强转更具可读性，更容易被查找
	int i = static_cast<int>(f); 
}
```
提高安全性

对于旧式类型转换，奉行了C语言的一贯作风：基本不进行任何逻辑判定，将灵活性和责任都丢给开发者。这样做的后果是可能会在某些比较难以察觉的地方埋下隐患，使用 static_cast 可以避免某些隐患。

```cpp
int main()
{
    int i = 6; // 普通整型数据
	float *pf; // 普通浮点指针

	// 旧式转换不进行任何合理性检查
	// 下面的代码，可以将类型问题瞒天过海
	// 编译器让其畅行无阻，开发者肉眼也难以察觉
	pf = (float *)&i; // float * 与 int * 不兼容，照样通过编译


	// 使用 static_cast 遇到非兼容性类型转换，会提出警告甚至错误
	pf = static_cast<float *>(&i); // float * 与 int * 不兼容
}
```


### 2.3 dymanic_cast

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
    // 类方法的const属性，可以形成重载
    void f3() const;
    void f3();
};

// 普通指针与常目标指针，可以形成重载
void f4(      char *p);
void f4(const char *p);

// 这两个函数签名不同，可以共存
void func(int &a);
void func(const int &a);
```

### 3.2 不可以形成重载的情形
- 函数名、函数参数列表完全一致。
- 函数的返回值类型差异。
- 静态函数声明（static）。
- const型变量（包括常指针）。
```cpp
// 函数名、参数列表完全一致，仅靠返回值类型
// 的差异，将无法形成重载，这两个函数将会冲突
void  f1(int a);
float f1(int a);

// static不能形成重载，以下两个函数将会冲突
       int f2(int a);
static int f2(int a);


// const型变量（包括常指针），不能形成重载
void f3(      int a);
void f3(const int a);

void f4(char *p);
void f4(char *const p);
```

### 3.3 引用形参
引用类型的参数是否可以形成重载，要根据实参的具体情况来定：

- 如果实参为常量，那么可以重载。
- 如果实参为变量，那么不可以重载。

```cpp
void f(int  a);
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

## 4. 左、右值传递参数(未)
- 左值（lvalue）：有名字、有地址、能持续存在的表达式。可以放在赋值号左边。
- 右值（rvalue）：临时值、字面量、即将销毁的表达式。不能取地址，通常只能放在赋值号右边。
```cpp
void func1(int &a);
void func2(const int &a);

int main()
{
    // 不可传递参数，100为常量，为右值
    func1(100);
    // 可以传递参数
    func2(100);

    // 不可以传递参数，a+1，为表达式，为右值
    int a=100;
    func1(a+1)
    //可以传递参数
    func2(a+1);

    // 可以传递参数
    func1(a); 
    func2(a); 
}
```
```cpp
void func1(int *a);
void func2(const int *a)

int main()
{
    int a=100;  
    int *p1=&a;
    const int *p2=&a;

    func1(p1);  // ✅ 正确
    func1(p2);  // ❌ 错误
    func2(p1);  // ✅ 正确
    func2(p2);  // ✅ 正确
}

## 5. &的本质(未)


