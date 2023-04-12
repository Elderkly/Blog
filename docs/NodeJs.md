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

#### SQL 异或写入

主要应对写后写的问题。

```sql
UPDATE incentive SET status=status^1 WHERE userId='ebf9262c'
```

```javascript
this.incentiveRepository
  .createQueryBuilder()
  .update(Incentive)
  .set({
    status: () => `status^${payTo === "inviter" ? 1 : 2}`,
  })
  .where("id = :id", { id })
  .execute();
```

#### SQL 筛选同一天或者同一月/周/季度/年

CURRENT_TIMESTAMP 有 BUG，对于`2023-03-21 11:04:19.670547`和`2023-03-22`的 day 比较会返回 0，所以比较前可以先将原数据格式化。

```sql
SELECT * FROM log WHERE TIMESTAMPDIFF(day, DATE_FORMAT(created, '%Y-%m-%d'), CURRENT_DATE()) = 0
```

```javascript
this.logRepository
  .createQueryBuilder("log")
  .select()
  .where(
    `TIMESTAMPDIFF(${unit}, created, CURRENT_TIMESTAMP) = 0 AND userId = :userId AND method = :method AND path = :path ORDER BY created DESC`,
    { userId, method, path }
  )
  .getOne();
```

**https://www.w3resource.com/mysql/date-and-time-functions/mysql-timestampdiff-function.php**

## NodeJs 生成表格

```javascript
import ExcelJS from "exceljs";
//  创建表格
const workbook = new ExcelJS.Workbook();
const sheet = workbook.addWorksheet("history");
//  写入数据
sheet.addRow([
  "utcData",
  "type",
  "currency",
  "amount",
  "fees",
  "address",
  "description",
]);
txs
  .concat(invoice, btcInvoice)
  .sort((a, b) => (b.time || b.timestamp) - (a.time || a.timestamp))
  .filter((e) => !!(e.amt || e.value))
  .forEach((e) => {
    sheet.addRow([
      new Date((e.time || e.timestamp) * 1000).toISOString(),
      e.type === "user_invoice" ? "CREDIT" : "DEBIT",
      "LIGHTNING",
      new BigNumber(e.amt || e.value).div(1e8).toNumber(),
      e.fee || 0,
      e.pay_req,
      e.memo || e.description,
    ]);
  });
//  设置样式
const amountCol = sheet.getColumn(4),
  addressCol = sheet.getColumn(6),
  descriptionCol = sheet.getColumn(7);
addressCol.alignment = amountCol.alignment = { horizontal: "right" };
descriptionCol.alignment = { horizontal: "fill" };
addressCol.width = 100;
descriptionCol.width = 80;
amountCol.width = 50;
const header = sheet.getRow(1);
header.alignment = { horizontal: "center" };
//  上传
const name = `lifpay_history_${id}_${Date.now()}`;
const buffer = await workbook.xlsx.writeBuffer();
const upload = await this.commonService.uploadFile({
  buffer,
  path: `${name}.xlsx`,
  originalname: `${name}.xlsx`,
});
```

**注意：csv 格式的表格无法设置样式**

## NodeJs 生成文件直接返回 buffer 文件流

```javascript
//  导出
const name = `lifpay_history_${id}_${Date.now()}.xlsx`;
const buffer = await workbook.xlsx.writeBuffer();

res.setHeader("Content-Disposition", `attachment;filename=${name}`);
res.setHeader(
  "Content-Type",
  "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;charset=utf-8"
);
res.end(buffer);
return res;
```

**https://www.cnblogs.com/taohuaya/p/14310307.html**

## NodeJs 生成表格存放在本地，返回下载链接

```javascript
const name = `lifpay_history_${id}_${Date.now()}.xlsx`;
const buffer = await workbook.xlsx.writeBuffer();
const file = path.join(process.cwd(), "public", "temp");
if (!fs.existsSync(file)) fs.mkdirSync(file);
fs.writeFileSync(`${file}/${name}`, buffer);

return {
  name,
  url: `${env.app.path}/temp/${name}`,
};
```

## Service 之间交叉引用会导致找不到对应的 Service

```javascript
@Service()
export class UserService {
  constructor(
    private commonService: CommonService,
  ) {}
  //......
}

@Service()
export class CommonService {
  constructor(
    private userService: UserService,
  ) {}
  //......
}
```

此时会导致 CommonService 找不到 UserService, 解决办法就是把公用代码写到一个起 Bus 作用的 Service 里，再从 CommonService 和 UserService 引用 BusService。

## 证书加密

**https://www.cnblogs.com/xdyixia/p/11610102.html**  
**https://blog.csdn.net/u013052238/article/details/81234898**

## 撤销最近的 commit

git reset --soft HEAD^
**https://www.cnblogs.com/lfxiao/p/9378763.html**

## Websocket 优化与封装 心跳

**https://juejin.cn/post/6945057379834675230**
