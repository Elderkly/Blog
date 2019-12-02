## interface接口
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
