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

#### 发送模板邮件

**http://automattic.github.io/juice/**
**https://juejin.cn/post/6903138530370715656**
**https://www.zhuimengzhu.com/details/149.html**

#### 接口限流

##### 全局

**https://github.com/nfriedly/express-rate-limit**

##### 局部

使用中间件  
**局部中间件要传参就在中间件中返回函数 调用时加括号 否则调用时不用加括号**

```javascript
//  /middleware/RequestLimitMiddleware.ts
import { Request, Response} from 'express'
import { redisClient } from '../../utils/redis'
interface LimitProps {
    max?: number        //  一段时间内最大请求次数
    minutes?: number    //  间隔时长
    message?: string    //  错误提醒
    redisKey?: string   //  与其它接口共享限额还是单独限额
}
export function RequestLimitMiddleware(options?: LimitProps) {
    return async function(request: Request, response: Response, next: (err?: any) => any) {
        const { minutes = 5, max = 10, message = 'Too many requests from this IP, please try again later.', redisKey = 'all'} = options ?? {}
        const ip = request.get("X-Real-IP") || request.get("X-Forwarded-For") || request.ip
        const num = Number(await redisClient.get(`rl_${ip}_${redisKey}`))
        if (!ip || ip === '::1') next()
        else if (!num || num < max) {
            await redisClient.setex(`rl_${ip}_${redisKey}`, 60 * minutes, num + 1)
            next()
        } else next(new Error(message))
    }
}

//  /controllers/indexController.ts
@Get('test')
@UseBefore(RequestLimitMiddleware())
public async test() {
    return 123
}
```

**https://github.com/typestack/routing-controllers/blob/develop/docs/lang/chinese/README.md#%E4%BD%BF%E7%94%A8%E4%B8%AD%E9%97%B4%E4%BB%B6**

#### findOne 的 BUG

findOne 当查询条件不匹配时默认会返回表中第一条数据，所以需要加入判断，当查询条件为空时直接返回空。
