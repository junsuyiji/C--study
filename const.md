# const

constL定义程序用到的数字，字符串常量，代替宏定义
const 定义的常量在预处理阶段并不存在，而是直到运行阶段才会出现

```cpp
const volatile int MAX_LEN

auto ptr = (int*)(&MAX_LEN);
*ptr = 2048;
cout<< MAX_LEN<<endl;
```


const 后面多出了一个 volatile 的修饰，它是这段代码的关键。如果没有
这个 volatile，那么，即使用指针得到了常量的地址，并且尝试进行了各种修改，但输出的
仍然会是常数 1024。
volatile 会禁止编译器做优化，所以除非必要，应当少用 volatile

基本的const用法，在 C++ 里，除了最基本的值类型，还有引用类型和指针类型，它们加上 const 就成了常量
引用和常量指针：
const用于指针的情况会略微复杂一点。常见的用法是，const 放在声明的最左边，表示指
向常量的指针。这个其实很好理解，指针指向的是一个“只读变量”，不允许修改

另外一种比较“恶心”的用法是，const 在“*”的右边，表示指针不能被修改，而指向的
变量可以被修改

const 成员函数”的意思并不是说函数不可修改。实际上，在 C++ 里，函数并不是变量
（lambda 表达式除外），所以，“只读”对于函数来说没有任何意义。它的真正含义是：
函数的执行过程是 const 的，不会修改对象的状态（即成员变量），也就是说，成员函数
是一个“只读操作

因为“常量引用”“常量指针”关联的对象是只读、不可修改的，那么也就意味着，对它的
任何操作也应该是只读、不可修改的，否则就无法保证它的安全性。所以，编译器会检查
const 对象相关的代码，如果成员函数不是 const，就不允许调用。

关键字 mutable

mutable 与 volatile 的字面含义有点像，但用法、效果却大相径庭。volatile 可以用来修
饰任何变量，而 mutable 却只能修饰类里面的成员变量，表示变量即使是在 const 对象
里，也是可以修改的。

总结：
1.const
它是一个类型修饰符，可以给任何对象附加上“只读”属性，保证安全；
它可以修饰引用和指针，“const &”可以引用任何类型，是函数入口参数的最佳类型；
它还可以修饰成员函数，表示函数是“只读”的，const 对象只能调用 const 成员函
数。
2.volatile
它表示变量可能会被“不被察觉”地修改，禁止编译器优化，影响性能，应当少用

3.mutable
它用来修饰成员变量，允许 const 成员函数修改，mutable 变量的变化不影响对象的常量
性，但要小心不要误用损坏对象。
你今后再写类的时候，就要认真想一想，哪些操作改变了内部状态，哪些操作没改变内部状
态，对于只读的函数，就要加上 const 修饰。写错了也不用怕，编译器会帮你检查出来。
总之就是一句话：尽可能多用 const，让代码更安全



code：

```cpp
#include <iostream>

void case1()
{
    using namespace std;

    //const int MAX_LEN       = 1024;
    const volatile int MAX_LEN       = 1024;
    const std::string NAME  = "metroid";

    auto ptr = (int*)(&MAX_LEN);
    *ptr = 2048;
    cout << MAX_LEN << endl;

}

void case2()
{
    using namespace std;

    int x = 100;

    const int& rx = x;
    const int* px = &x;

    cout << rx << *px << endl;

    string name = "uncharted";

    const string* ps1 = &name;
    //*ps1 = "spiderman";

    cout << *ps1 << endl;

    string* const ps2 = &name;
    *ps2 = "spiderman";

    cout << *ps2 << endl;

    const string* const ps3 = &name;
}

class DemoClass final
{
private:
    using mutex_type = int; // dummy type
private:
    mutable mutex_type  m_mutex;

    const long  MAX_SIZE = 256;
    int         m_value;
public:
    int get_value() const
    {
        return m_value;
    }

    void save_data() const
    {
        m_mutex++;
    }
};


constexpr
int fib(int n)
{
    if (n == 0 || n == 1) {
        return 1;
    }

    return fib(n - 1) + fib(n - 2);
}

int main()
{
    using namespace std;

    case1();
    case2();

    constexpr
    int fib5 = fib(5);

    cout << fib5 << endl;

    cout << "const demo" << endl;
}
```

