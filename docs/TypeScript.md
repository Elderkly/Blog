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
```
