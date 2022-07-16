## 控制台输出

### 类 C 语言输出

`console.log('我的%s已经%d岁', '猫', 2)`

- `%s` 会格式化变量为字符串
- `%d` 会格式化变量为数字
- `%i` 会格式化变量为其整数部分
- `%o` 会格式化变量为对象

### 堆栈踪迹

`console.trace()`

### 计算耗时

`console.time()`和`console.timeEnd()`

### 进度条

```javascript
const ProgressBar = require("progress");

const bar = new ProgressBar(":bar", { total: 10 });
const timer = setInterval(() => {
  bar.tick();
  if (bar.complete) {
    clearInterval(timer);
  }
}, 100);
```

### package.json 与 package-lock.json

package.json 只能锁定模块的大版本号（版本号的第一位），不能锁定后面的小版本，所以你每次重新 npm install 时候拉取的都是该大版本下面最新的版本。
package-lock.json 文件锁定所有模块的版本号，包括主模块和所有依赖子模块。package-lock.json 目的就是确保所有库包与你上次安装的完全一样。

### npx

npx 可以运行使用 Node.js 构建并通过 npm 仓库发布的代码。无需先安装命令即可运行命令。  
例：`npx create-react-app my-react-app`

### `process.nextTick()`与`setImmediate()`

`process.nextTick()`异步微任务，`setImmediate()`类似于`setTimeout(() => {},0)`
