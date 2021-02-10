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
`定义`:namespace是指标识符的各种可见范围。命名空间用关键字namespace 来定义。命名空间是C++的一种机制，用来把单个标识符下的大量有逻辑联系的程序实体组合到一起。此标识符作为此组群的名字。   
**不使用using namespace**
```c++
int main()
{
    std::cout << "hello world" << std::endl;//
    return 0;
}
```
**使用using namespace**
```c++
using namespace std;//使用标准命名空间
int main()
{
    cout << "hello world" << endl;//此处也可以用\n代替endl
    return 0;
}
```
这样命名空间std内定义的所有标识符都有效（曝光）。就好像它们被声明为全局变量一样。   

