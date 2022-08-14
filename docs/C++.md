### 输入输出

```c++
#include <iostream>
using namespace std;
int main(int argc, const char * argv[]) {
    int a;
    cout<<"请输入一个数字，按回车结束"<<endl;
    cin>>a;
    cout<<a<<endl;
    cout<<"8进制"<<oct<<a<<endl;     //  8进制
    cout<<"10进制"<<dec<<a<<endl;     //  10进制
    cout<<"16进制"<<hex<<a<<endl;     //  16进制

    //  输出布尔型变量
    bool f = false;
    cout<<f<<endl;              //    0
    cout<<boolalpha<<f<<endl;   //    false
    return 0;
}
```

### using namespace

`定义`:namespace 是指标识符的各种可见范围。命名空间用关键字 namespace 来定义。命名空间是 C++的一种机制，用来把单个标识符下的大量有逻辑联系的程序实体组合到一起。此标识符作为此组群的名字。  
**不使用 using namespace**

```c++
int main()
{
    std::cout << "hello world" << std::endl;//
    return 0;
}
```

**使用 using namespace**

```c++
using namespace std;//使用标准命名空间
int main()
{
    cout << "hello world" << endl;//此处也可以用\n代替endl
    return 0;
}
```

这样命名空间 std 内定义的所有标识符都有效（曝光）。就好像它们被声明为全局变量一样。

### #define

`#define` 是一种预处理命令，用于定义宏，本质上是文本替换。

```c++
#include <iostream>
#define n 233

// n 不是变量，而是编译器会将代码中所有 n 文本替换为 233，但是作为标识符一部分的
// n 的就不会被替换，如 fn 不会被替换成 f233，同样，字符串内的也不会被替换

int main() {
  std::cout << n;  // 输出 233
  return 0;
}
```

**使用 #define 要小心**

```c++
#include <iostream>
#define sum(x, y) x + y
// 这里应当为 #define sum(x, y) ((x) + (y))
#define square(x) ((x) * (x))

int main() {
  std::cout << sum(1, 2) << ' ' << 2 * sum(3, 5) << std::endl;
  // 输出为 3 11，因为 #define 是文本替换，后面的语句被替换为了 2 * 3 + 5
  int i = 1;
  std::cout << square(++i) << ' ' << i;
  // 输出未定义，因为 ++i 被执行了两遍
  // 而同一个语句中多次修改同一个变量是未定义行为（有例外）
}
```
