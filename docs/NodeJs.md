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

## routing-controllers 定义可选接口

```typescript
  @Get('/stat/user/:year/:month?')
  public async statisticalUserDataByDate(@Params() params: {year: string, month?: string}) {
        const { year, month } = params
        return await this.systemServices.analyzeUserData(year, month)
    }
```

## 复杂 sql 分析

```sql
SELECT
  months.month,
  IFNULL(counts.count, 0) AS count,
  IFNULL(daily_counts.active, 0) AS active
FROM
  (
    SELECT 1 AS month
    UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5 UNION SELECT 6
    UNION SELECT 7 UNION SELECT 8 UNION SELECT 9 UNION SELECT 10 UNION SELECT 11
    UNION SELECT 12
  ) AS months
LEFT JOIN
  (
    SELECT YEAR(created) AS year, MONTH(created) AS month, COUNT(*) AS count
    FROM user
    WHERE YEAR(created) = ${year}
    GROUP BY YEAR(created), MONTH(created)
  ) AS counts ON months.month = counts.month
LEFT JOIN
  (
    SELECT YEAR(created) AS year, MONTH(created) AS month, COUNT(*) AS active
    FROM log
    WHERE YEAR(created) = ${year} AND path = '/api/context'
    GROUP BY YEAR(created), MONTH(created)
  ) AS daily_counts ON months.month = daily_counts.month
ORDER BY months.month;
```

### IFNULL(a,b)

如果 a 为 null 则为 b

### UNION

UNION 是用于合并两个或多个 SELECT 语句的结果集的运算符。它会将多个 SELECT 语句的结果按照列的顺序进行合并，形成一个包含所有结果的新结果集。  
在这个例子中，SELECT 1 AS month 表示第一个 SELECT 语句，它会返回一个结果集，其中只包含一个列 month，并将其值设为 1。然后，使用 UNION 运算符将后续的 SELECT 语句依次合并在一起。每个额外的 SELECT 语句都会返回一个结果集，其中只包含一个列 month，并分别将其值设为 2、3、4、...、12。最终，通过使用 UNION 运算符将所有结果集合并在一起，形成包含 1 到 12 月份的结果集。

### LEFT JOIN

LEFT JOIN 是一种连接操作，用于将左边的表与右边的表进行连接。在这个查询中，months 表是左边的表，而 counts 表和 daily_counts 表是右边的表。LEFT JOIN 会返回左边表中的所有行，并根据连接条件将右边表中匹配的行合并在一起。如果右边表中没有匹配的行，对应的列将包含 NULL 值。在这个查询中，通过 LEFT JOIN 将月份数据和对应的统计数据连接在一起，确保即使某个月份的数据不存在，仍然能够在结果中显示该月份，并将对应的统计数据设为 0。

**https://blog.csdn.net/u011047968/article/details/107744901**  
**https://blog.csdn.net/weixin_39220472/article/details/81193617**

```sql
SELECT DATE_FORMAT(DATE_ADD('${year}-${month}-01', INTERVAL(CAST(help_topic_id AS signed integer)+ 0) day),'%Y-%m-%d') date
  FROM mysql.help_topic
  WHERE help_topic_id < DAY(LAST_DAY('${year}-${month}-01'))
  ORDER BY help_topic_id;
-- 简化版
SELECT DATE_FORMAT(DATE_ADD('${year}-${month}-01', INTERVAL help_topic_id DAY),'%Y-%m-%d') date
  FROM mysql.help_topic
  WHERE help_topic_id < DAY(LAST_DAY('${year}-${month}-01'))
  ORDER BY help_topic_id;
```

### INTERVAL 和 INTERVAL()

INTERVAL 常与时间计算函数结合使用

#### 作为关键字使用

直接计算时间间隔  
`INTERVAL expr unit`

```sql
SELECT NOW()-INTERVAL '2' HOUR;
SELECT INTERVAL help_topic_id DAY;
```

#### 作为函数使用

用于在日期和时间值上执行加法运算

```sql
SELECT INTERVAL (CAST(help_topic_id AS signed integer) + 0) DAY
```

### CAST

类型转换  
`CAST(expression AS TYPE);`

| 目标类型         | 示例                                            | 说明                                              |
| ---------------- | ----------------------------------------------- | ------------------------------------------------- |
| SIGNED INTEGER   | `CAST(expression AS SIGNED INTEGER)`            | 转换为有符号整数类型                              |
| UNSIGNED INTEGER | `CAST(expression AS UNSIGNED INTEGER)`          | 转换为无符号整数类型                              |
| DECIMAL          | `CAST(expression AS DECIMAL(p, s))`             | 转换为定点小数类型，其中 p 是总位数，s 是小数位数 |
| FLOAT            | `CAST(expression AS FLOAT)`                     | 转换为浮点数类型                                  |
| CHAR             | `CAST(expression AS CHAR(n))`                   | 转换为定长字符串类型，n 是指定的长度              |
| VARCHAR          | `CAST(expression AS VARCHAR(n))`                | 转换为可变长度字符串类型，n 是指定的长度          |
| DATE             | `CAST(expression AS DATE)`                      | 转换为日期类型                                    |
| TIME             | `CAST(expression AS TIME)`                      | 转换为时间类型                                    |
| DATETIME         | `CAST(expression AS DATETIME)`                  | 转换为日期时间类型                                |
| BOOLEAN          | `CAST(expression AS BOOLEAN)`                   | 转换为布尔类型                                    |
| BINARY           | `CAST(expression AS BINARY(n))`                 | 转换为二进制类型，n 是指定的长度                  |
| VARBINARY        | `CAST(expression AS VARBINARY(n))`              | 转换为可变长度二进制类型，n 是指定的长度          |
| JSON             | `CAST(expression AS JSON)`                      | 转换为 JSON 类型                                  |
| ENUM             | `CAST(expression AS ENUM('value1', 'value2'))`  | 转换为枚举类型，允许的值在列表中选择              |
| SET              | `CAST(expression AS SET('option1', 'option2'))` | 转换为集合类型，允许的选项在列表中选择            |

### DATE_ADD

将时间间隔添加到起始时间,常结合 INTERVAL 使用  
`DATE_ADD(date,INTERVAL expr type)`

```sql
DATE_ADD('2023-02-01', INTERVAL help_topic_id DAY)
DATE_ADD('2023-02-01', INTERVAL(CAST(help_topic_id AS signed integer)+ 0) DAY)
```

### DAY(LAST_DAY('${year}-${month}-01'))

DAY 获取日期的天数  
LAST_DAY 获取某一月份的最后一天

## varchar 和 text

在 SQL 中，text 字段和 varchar 字段都是用于存储文本类型数据的字段，但是它们之间有一些区别。

varchar 字段是一种可变长度的字符数据类型，可以存储长度可变的字符串。例如，如果一个 varchar 字段被定义为 varchar(50)，那么它可以存储长度最长为 50 个字符的字符串。如果字符串的长度小于 50，则该字段只会占用实际字符串的字节数加上一些额外的控制信息。因此，varchar 类型比 text 类型更适合存储短文本数据。

text 字段则是一种专门用于存储大型文本数据的数据类型。它可以存储长度非常长的文本数据，可以达到几千或几百万个字符。text 类型的字段不需要指定长度，因为它们可以存储任意长度的文本数据。由于 text 类型的字段需要占用较大的存储空间，因此在使用时需要考虑到性能和存储空间的问题。

关于“text 字段是存在表外的”这句话的意思是，如果一个表中包含大量的 text 类型的字段，那么这些字段的实际数据可能不会直接存储在表格中，而是存储在其他的独立的文件或数据块中。这是因为 text 类型的数据可能非常大，直接存储在表格中会导致表格的大小快速增长，而且查询和操作这些数据的效率也会受到影响。因此，数据库管理系统通常会将 text 类型的数据存储在外部文件中，以提高查询和操作的性能。虽然这些数据不直接存储在表格中，但是数据库管理系统会维护它们与表格中的其他数据之间的关系，以确保数据的完整性和一致性。

## 测试服务器上的两个项目无法通过域名访问

### 情景分析

> 1.两个项目部署在同一个服务器中的不同端口 并且分别配置了 https 证书和域名  
> 2.在外网通过域名可以正确访问 内网通过 ip 可以访问  
> 3.在其中一个项目使用另一个项目的域名无法访问 使用内网 ip 进行访问正常  
> 4.使用 nginx 做了反向代理

### Nginx 正向代理和反向代理

#### 正向代理

使内网服务器可以访问外网资源

#### 反向代理

使外网可以访问到内网资源

正向代理服务器与反向代理服务器的概念很简单，归纳起来就是，正向代理服务器用来让局域网客户机接入外网以访问外网资源，反向代理服务器用来让外网的客户端接入局域网中的站点以访问站点中的资源。理解这两个概念的关键是要明白我们当前的角色和目的是什么，在正向代理服务器中，我们的角色是客户端，目的是要访问外网的资源；在反向代理服务器中，我们的角色是站点，目的是把站点的资源发布出去让其他客户端能够访问。

**https://www.cnblogs.com/chenqionghe/p/10426084.html**

### 原因

**内网服务器默认只能访问内网资源 如果需要访问外网资源需要中间件**  
在外网通过域名访问正常，说明 nginx 的反向代理是没问题的， 用于将外部请求转发到内网服务器。  
在其中一个项目使用内网 ip 可以访问另一个项目，同一个内网的资源默认是可以互相访问的。  
可能得原因  
1.从服务器上解析 B 的 DNS 失败  
2.防火墙是否拦截了内网间的请求  
3.nginx 配置错误

## typeorm 插入 emoji 时报错 ER_TRUNCATED_WRONG_VALUE_FOR_FIELD

### 分析

这个问题可能是因为 MySQL 默认使用的字符集是 `utf8`，而不是 `utf8mb4`。`utf8` 只支持三字节的 Unicode 字符，而不支持四字节的 Emoji 字符。因此，当你尝试插入四字节的 Emoji 字符时，MySQL 报错。

在 MySQL 中，要支持四字节的 Unicode 字符，需要使用 `utf8mb4` 字符集。因此，当你在数据库客户端使用 `insert` 或 `update` 语句插入 Emoji 时，可能已经使用了 `utf8mb4` 字符集，所以没有报错。

使用 TypeORM 插入 Emoji 字符时，如果没有设置字符集为 `utf8mb4`，则会默认使用 MySQL 的默认字符集 `utf8`，导致插入四字节的 Emoji 字符时报错。因此，你需要在 TypeORM 中设置字符集为 `utf8mb4`，才能插入四字节的 Emoji 字符。

在你的调整插入的代码中，使用 `await this.userRepository.query("SET NAMES 'utf8mb4'")` 设置了字符集为 `utf8mb4`，所以在 TypeORM 中插入 Emoji 字符时就没有报错了。

`SET NAMES 'utf8mb4'` 命令告诉 MySQL 使用 `utf8mb4` 字符集，它会设置三个 MySQL 会话级别的变量：`character_set_client`、`character_set_results` 和 `character_set_connection`，用于指示 MySQL 在会话中使用哪个字符集。这样，当你在 TypeORM 中插入 Emoji 字符时，MySQL 就会使用 `utf8mb4` 字符集，从而支持四字节的 Emoji 字符。

### 如何查询数据库的默认字符集

```sql
SHOW VARIABLES LIKE 'character_set_server';
```

或

```sql
SHOW VARIABLES LIKE 'character_set%';
```

### 修改数据库默认字符集

可以通过修改 MySQL 服务器的配置文件来修改默认字符集。默认情况下，MySQL 服务器使用的字符集为 `latin1`，你可以将其修改为 `utf8mb4`。

以下是具体的步骤：

1. 打开 MySQL 服务器的配置文件 `my.cnf` 或 `my.ini`，这个文件通常位于 MySQL 安装目录下的 `etc` 目录中。

2. 找到 `[mysqld]` 段落，在其中添加以下两行配置：

   ````
   [mysqld]
   character_set_server = utf8mb4
   collation_server = utf8mb4_unicode_ci
   ```

   第一行配置设置 MySQL 服务器的默认字符集为 `utf8mb4`，第二行配置设置默认的排序规则为 `utf8mb4_unicode_ci`。你可以根据需要修改排序规则。

   ````

3. 保存文件并重启 MySQL 服务器，让修改生效。

重启 MySQL 服务器后，它的默认字符集就会被修改为 `utf8mb4`，这样就可以支持插入和存储四字节的 Unicode 字符，包括 Emoji 字符了。

需要注意的是，修改 MySQL 服务器的默认字符集会影响所有新创建的数据库和表。如果你已经有现有的数据库和表，并且它们使用了不同的字符集，那么你需要分别修改它们的字符集和排序规则，以保持一致性。

### 编码

- Unicode: Unicode 是一种字符集，包含了全球所有已知字符的唯一标识符。通常用十六进制数表示。UTF-8 和 UTF-16 是两种常用的 Unicode 编码方案，用于将 Unicode 字符集中的字符编码成字节序列以便在计算机系统中传输和存储。
- UTF-8: 一种编码方式，它可以用来表示 Unicode 字符集中的所有字符。UTF-8 编码采用一种变长编码方式，即根据字符的码点大小，来决定该字符的 UTF-8 编码所占用的字节数。
- utf8: MySQL 数据库中的一种字符集，它可以存储使用 UTF-8 编码的字符数据，其中一个字符可以占用 1 到 3 个字节不等的空间。
- utf8mb4: MySQL 数据库中的一种字符集，它可以存储使用 UTF-8 编码的字符数据，其中一个字符可以占用 1 到 4 个字节不等的空间。相比于 utf8，utf8mb4 可以支持更多的 Unicode 字符，包括一些较少使用的字符，如一些汉字、表情符号、符号等。
- utf8mb4_general_ci: 一种比较简单的排序规则，它只考虑字符的字节序列，而不考虑字符的语言、大小写、重音符等差异。这意味着一些相似的字符可能被认为是相等的，但一些不同的字符也可能被认为是相等的。例如，字母"a"和字母"ä"可能被认为是相等的，但是字符"ß"和字符"ss"可能被认为是不等的。
- utf8mb4_unicode_ci: 一种更为复杂的排序规则，它考虑了字符的语言、大小写、重音符等差异。它使用 Unicode 标准中的排序算法来比较字符，可以确保相似的字符被认为是相等的，不同的字符被认为是不等的。例如，字母"a"和字母"ä"被认为是不同的字符，而字符"ß"和字符"ss"被认为是相等的。

ci 代表 case-insensitive，即不区分大小写。

**https://apps.timwhitlock.info/emoji/tables/unicode**
