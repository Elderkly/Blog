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

## MQ 队列模式

区分生产者/消费者模式和发布/订阅模式。  
**https://juejin.cn/post/7111741311920635912**  
**https://zeromq.org/languages/nodejs/**

## 外键约束与唯一性约束

当你在主表中给 hash 字段添加了唯一性约束后，如果你将该字段用作外键在子表中建立关联，这个唯一性约束也会自动应用于子表中的 hash 字段，使得子表中的 hash 字段也不允许重复值。

## ZeroMQ

发布者的订阅地址必须是本机的内/外/环网地址，订阅者的订阅地址没有限制。

## Params 和 QueryParams 的区别

@Params() 装饰器和 @QueryParams() 装饰器在使用上有以下区别：

@Params() 装饰器用于获取路径参数（Path Parameters），即从路径中提取的参数。例如，如果您的路由定义为 /users/:id，那么可以使用 @Params() 装饰器来获取 id 参数的值。

@QueryParams() 装饰器用于获取查询参数（Query Parameters），即请求链接中的参数。这些参数通常以 ? 开头，并以 key=value 的形式出现在 URL 中。例如，?userId=123&name=John。

## 内联查询可能影响分页 leftJoinAndSelect

内联查询可能会导致查询结果的行数发生变化，从而影响到分页的计算。在进行内联查询时，可能会涉及多个表格，这可能导致结果集中的行数与实际查询的行数不一致。

如果你需要进行内联查询，并且希望在结果中包含内联查询的字段，你可以尝试使用子查询来代替内联查询。子查询可以将内联查询的结果作为独立的查询，并在主查询中引用它们。

通过将内联查询转换为子查询，你可以单独执行内联查询，并将其结果映射到主查询的结果中。这样可以避免内联查询对分页功能的影响。

## SQL 中 LIMIT 语句要在 ORDER BY 之后

## 复杂查询

```typescript
public async queryTransactionEntity(findOptions: queryTransactionEntityParams) {
  console.log(findOptions)
  const { userId, startDate, endDate, minAmount, maxAmount, page, pageSize = 15, inner } = findOptions

  const optionsQuery: string[] = []
  // userId筛选
  !!userId && (optionsQuery.push(`transaction_entity.entityId = '${userId}'`))

  // 时间筛选
  !!startDate && optionsQuery.push(`transaction_entity.created >= '${startDate}'`);
  !!endDate && optionsQuery.push(`transaction_entity.created < DATE_ADD('${endDate}', INTERVAL 1 DAY)`);

  // amount字段的查询条件
  !!minAmount && optionsQuery.push(`transaction_entity.amount >= ${minAmount}`)
  !!maxAmount && optionsQuery.push(`transaction_entity.amount <= ${maxAmount}`)

  //  内部数据
  inner !== undefined && inner !== '' && optionsQuery.push(`inner_query.hash IS ${inner === '1' ? 'NOT' : ''} NULL`)

  const getQuery = (SELECT: string, LIMIT: string = '') => `
    SELECT
      ${SELECT}
    FROM
      transaction_entity
    LEFT JOIN
      transaction ON transaction.hash = transaction_entity.hash
    LEFT JOIN
    (
        SELECT
            inner_entity.hash,
            COUNT(*) AS isinner
        FROM
            transaction_entity AS inner_entity
        GROUP BY
            inner_entity.hash
        HAVING
            COUNT(*) > 1
    ) AS inner_query ON inner_query.hash = transaction_entity.hash
    ${optionsQuery.length > 0 ? 'WHERE' : ''}
      ${optionsQuery.join(' AND ')}
    ORDER BY
      transaction_entity.created DESC
    ${LIMIT}
  `;

  const recordQuery = getQuery(`
    transaction_entity.*,
    transaction.fee,
    transaction.network,
    transaction.remark,
    CASE WHEN inner_query.isinner IS NOT NULL THEN 1 ELSE 0 END AS isinner
  `, page ? `LIMIT ${(page - 1) * pageSize}, ${pageSize}` : '')
  console.log(recordQuery)
  const countQuery = getQuery(`COUNT(*) as totalCount`)
  console.log(countQuery)

  return {
    totalCount: (await this.transactionEntityRepository.query(countQuery))?.[0]?.totalCount || 0,
    data: await this.transactionEntityRepository.query(recordQuery)
  }
}
```
