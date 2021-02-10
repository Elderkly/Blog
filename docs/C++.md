#### 输出
`cout<<x<<endl;`   
`cout<<"x+y="<<x+y<<endl;`
#### 输入
`cin>>a;`   
`cin>>a>>b`  
#### using namespace
`定义`:namespace是指标识符的各种可见范围。命名空间用关键字namespace 来定义。命名空间是C++的一种机制，用来把单个标识符下的大量有逻辑联系的程序实体组合到一起。此标识符作为此组群的名字。   
不使用using namespace
```c++
int main()
{
    std::cout << "hello world" << std::endl;//
    return 0;
}
```
使用using namespace
```c++
using namespace std;//使用标准命名空间
int main()
{
    cout << "hello world" << endl;//此处也可以用\n代替endl
    return 0;
}
```
这样命名空间std内定义的所有标识符都有效（曝光）。就好像它们被声明为全局变量一样。   

