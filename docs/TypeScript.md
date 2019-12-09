# interface接口
```typescript
//  可选属性
interface Point {
    y: number
    x?: string
}

//  只读属性
interface Point{
    readonly x: number
    readonly y: number
}
let roArr: ReadonlyArray<number> = [1,2,3,4];      //   只读数组类型
roArr[0] = 1    //   error

//  任意属性
interface Point {
    color: string
    width: number
    [propName: string]: any
}

//  索引类型
interface IPoint {
  [index: number]: string
}
const arr: IPoint = ["aa","bb"]     //  只要传入索引是number类型 返回的值都为string

//  类类型
interface ClassPoint {
  currentTime: Date
  getTime(d: Date): void
}
class A implements ClassPoint {
    currentTime: Date = new Date();
    getTime(d:Date):void {
        this.currentTime = d
    }
}

//  接口继承
interface Point1 {
    color: string
}
interface Point2 {
    width: number
}
interface Point3 extends Point1,Point2{
    height:number
}
/**
    Point3 = {
        color: string
        width: number
        height: number
    }   
*/
```
**当接口继承了一个类类型时，它会继承类的成员但不包括其实现。      
就好像接口声明了所有类中存在的成员，但并没有提供具体实现一样。      
接口同样会继承到类的private和protected成员。       
这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）。**    

# class类
```typescript
//  继承
class Animal {
    name: string
    constructor(thisName: string) {
        this.name = thisName 
    }
    move(num: number = 0) {
        console.log(`${this.name}移动了${num}米`)
    }
}

class Dog extends Animal{
    constructor(name: string) {
        super(name)
    }
    move(num: number) {
        console.log("重写move方法后调用父类move方法")
        super.move(num)
    }
}

const dog: Animal = new Dog("tim")
dog.move(8)

//  权限
class Animal2 {
    //  公用
    public name: string
    //  私有
    private move(name: string) {}
    /**
        与private类似 外部不能访问 子类可以访问
        可以将构造函数指定为protected
        这样类将不能被实例化但能被继承
    */  
    protected jump(num: number) {}
    /**
        readonly 只读属性
        只读属性必须在声明时或构造函数里被初始化
    */
    readonly age: number
    readonly six: number = 0
    //  在构造函数中创建并赋值私有成员
    constructor(private thisAge: number) {
        this.age = thisAge
    }
}

//  存取器
const passcode = '1234'
class Employee {
    private _fullName: string
    get fullName(): string {
        return this._fullName
    }
    set fullName(newName: string) {
        if (passcode === '123456') {
            this._fullName = newName
        } else {
            console.log("密码有误")
        }   
    }   
}

const employee = new Employee()
employee.fullName = "ABCD"

//  静态属性
class Grid {
    static name: string = 'tom'
    print() {
        console.log(this.name)
    }
    static _print() {
        console.log(this.name)
    }
}

Grid._print()   //  tom
const a = new Grid()
a.print()       //  undefined

//  抽象类
/**
    抽象类只能被继承不能被实例化
    抽象类中的抽象方法必须在派生类中实现
*/
abstract class ABS {
    constructor(public name: string) {}
    abstract move(num: number): void
}

class ABS2 extends ABS {
    move(num:number) {
        console.log("MOVE")
    }
}
```

# 函数
```typescript
//  定义函数类型
const myFn: (x: number, y: number) => number = 
    function(x: number, y: number): number { return x + y }
```

# 泛型
```typescript
function logging<T>(arg: T[]): T[] {
    return arg
}
logging<number>([1,2,3])

//  泛型接口
interface IdentityFn {
  <T>(arg: T): T;
}
let myFn: IdentityFn = logging

//  泛型类
class GenericNumber<T> {
    zeroValue: T
    add: (x: T, y: T) => T
}

const class1 = new GenericNumber<number>()
class1.zeroValue = 0
class1.add(9,1)
```
