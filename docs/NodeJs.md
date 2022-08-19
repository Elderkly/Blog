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

### 清除 require 缓存

**https://newsn.net/say/commonjs-require-cache.html**

### 双重认证算法(双因子认证)

HOTP: 请求双方传递计数器 C 进行同步。  
TOTP: 根据时间戳进行同步，双方需保证时间戳相等。

#### TOTP 的刷新周期如何保证

```javascript
T = Math.floor(Date.now() / 1000 / 30);
```

得出的结果为当前时间戳等于多少个 30 秒的周期，时间戳每过 30 秒都会产生一个新的时间周期 T，以此实现双端的同步。

#### TOTP 原理图

![TOTP原理](https://bbs-img.huaweicloud.com/blogs/img/1604456601669089716.png)

**https://www.rfc-editor.org/rfc/rfc4226**  
**https://bbs.huaweicloud.com/blogs/205528**

#### 上传图片

```javascript
   @Post('test')
    public async test(@UploadedFile('file') files: any) {
        console.log(files)
        return files
    }
```
