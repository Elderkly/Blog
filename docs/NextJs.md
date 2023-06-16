# Next.js

### 几种渲染方式

|     |     名称     | 解释                                                             |
| :-: | :----------: | :--------------------------------------------------------------- |
| BSG |  客户端渲染  | 类 Vue、React 去创建 HTML                                        |
| SSG | 静态页面生成 | 将后端数据获取后，将页面打包成静态页面进行渲染，在编译时进行工作 |
| SSR |  服务端渲染  | 页面运行时请求后端接口后，拼接完数据再进行展示，在页面运行时工作 |

### 实现国际化的两种方案

#### 1.Next-i18next

在根目录的`next.config.js`配置后，在每个页面的`getStaticProps`函数都需要执行`...(await serverSideTranslations(locale, ['common', 'footer'])),`  
动态路由需包含`loacle`属性:

```typescript
export const getStaticPaths = ({ locales }) => {
  return {
    paths: [
      // if no `locale` is provided only the defaultLocale will be generated
      { params: { slug: "post-1" }, locale: "en-US" },
      { params: { slug: "post-1" }, locale: "fr" },
    ],
    fallback: true,
  };
};
```

切换语言：

```typescript
router.push({ pathname, query }, asPath, { locale: nextLocale });
```

\*_缺点：无法使用`next export`，部署后需要`nodejs`支持_

**https://github.com/isaachinman/next-i18next**  
**https://nextjs.org/docs/advanced-features/i18n-routing**

#### 2.根目录为语言的动态路由

项目结构调整如下：  
![结构](https://github.com/Elderkly/ImgRepository/blob/master/Blog/Nextjs-1.png)  
所有页面都放在`[lang]`之下，当访问页面时，先进入到`[lang]`级路由，确认完语言后再进入到相应页面。

\_app.tsx

```typescript
import i18next from "i18next";
import { useRouter } from "next/router";
import { defaultLanguage, languages } from "../i18n";
// import ...
const App = ({ Component, pageProps }: AppProps) => {
  let language;
  const router = useRouter();
  const { asPath, query } = router;
  if (query.lang) {
    language = query.lang;
  } else {
    const slug = asPath.split("/")[1];
    const index = languages.findIndex((e) => e.translation === slug);
    const langSlug = index !== -1 ? slug : defaultLanguage;
    language = langSlug;
  }
  if (asPath !== "/" && asPath !== "/404") {
    i18next.changeLanguage(language);
  }

  return (
      //    ...
  );
};
export default App;
```

每个页面都需要配置动态路由：

```typescript
export function getAllLanguageSlugs() {
  return languages.map((lang) => {
    return { params: { lang: lang.translation } };
  });
}

export function getLanguage(lang) {
  const index = languages.findIndex((e) => e.translation === lang);
  return index !== -1 ? lang : defaultLanguage;
}

export async function getStaticPaths() {
  const paths = getAllLanguageSlugs();
  return {
    paths,
    fallback: false,
  };
}

export async function getStaticProps({ params }) {
  const language = getLanguage(params.lang);
  return {
    props: {
      language,
    },
  };
}
```

跳转页面与切换语言

```typescript
//  跳转页面
router.push(`/${router.query.lang}/list/`);
//  切换语言
router.push({
  pathname: router.pathname,
  query: { ...router.query, lang: item.translation },
});
```

**此方案支持`next export`，打包后正常部署即可。**

**https://github.com/Elderkly/nextjs-demo/tree/221c1ca5ab4f7b8775053fa65325794eb34811ca**

### Nextjs12 配置 antd

**https://github.com/elado/next-with-less**

### Nextjs12 配置环境变量

**https://nextjs.org/docs/basic-features/environment-variables**  
**https://segmentfault.com/a/1190000038279352**

### 使用 SSR 后无法动态生成 meta

删除 redux 后解决

### redux-persist 会影响 meta 标签生成

**https://stackoverflow.com/questions/72966026/issue-with-meta-tags-when-using-redux-persist-in-next-js**

### SSR 模式下无法获取 document

**https://stackoverflow.com/questions/60629258/next-js-document-is-not-defined**

### NextJS 如果 nginx 做了反向代理 需关闭 trailingSlash 配置

trailingSlash 会默认在链接后加/符号，在 nginx 中会指向某一文件夹，可能造成 404

### NextJS 配置请求资源前缀

assetPrefix

### 测试 metadata

**https://developers.facebook.com/tools/debug/**

### 缓存

#### 针对静态资源配置缓存需要在根目录创建 vercel.json 文件

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "Cache-Control",
          "value": "public, max-age=600, immutable"
        }
      ]
    }
  ]
}
```

#### 针对 SSR 做缓存

```typescript
export async function getServerSideProps(context: GetServerSidePropsContext) {
  const {
    res,
    query: { t, id },
  } = context;
  res.setHeader(
    'Cache-Control',
    'public, s-maxage=30, stale-while-revalidate=300'
  );
  ...
  return {
    props: {
      ...
    }
  };
}
```

相关字段说明：

- public：表示响应可以被公共缓存（例如 CDN）缓存。
- s-maxage=10：表示在 10 秒内，响应被视为新鲜（fresh）。在这个时间范围内，重复的请求将使用之前缓存的值而不是向后端服务器发起新的请求。
- stale-while-revalidate=59：表示在 59 秒内，即使缓存的值已过期（stale），仍然可以使用该值进行渲染，同时后台会发起重新验证的请求来更新缓存的值。
- immutable: 指令表示资源在指定时间段内是不可变的，因此浏览器和 CDN 可以安全地缓存资源并在缓存过期之前不再验证。
- must-revalidate: 指令要求浏览器在每次使用缓存资源之前都向服务器发送验证请求，以确保资源是否仍然有效。

### 针对中国大陆无法访问 vercel.com 资源

vercel.com 的 DNS 被污染，配置一个中国大陆可以访问的域名可解决。

**https://github.com/orgs/vercel/discussions/803**  
**https://vercel.com/docs/concepts/projects/domains**

#### 配置域名

只能在 dns 服务器上进行配置:

- 验证你对该域名的控制权 配置 TEXT 记录（TXT Record：
  TEXT 记录是一种 DNS 记录类型，用于存储与域名相关的文本信息。在 Vercel 的情况下，你需要在 DNS 设置中创建一个 TEXT 记录，并将其值设置为 Vercel 提供的特定文本字符串。这个 TEXT 记录的作用是验证你对该域名的控制权，确保你有权将该域名与 Vercel 的服务进行关联。
- 建立域名的别名关系 配置 CNAME 记录（Canonical Name Record）：
  CNAME 记录也是一种 DNS 记录类型，用于建立域名的别名关系。在 Vercel 上配置 CNAME 记录的目的是将你的自定义域名指向 Vercel 的服务。你需要在 DNS 设置中创建一个 CNAME 记录，并将别名（自定义域名）指向 Vercel 提供的目标地址（通常是一个 Vercel 提供的域名）。

#### 原因

当你直接访问 abc.vercel.com 时，请求会直接发送到 Vercel 的服务器，但由于该域名的 DNS 被污染，DNS 解析可能会失败或导致资源加载超时或连接被拒绝的问题。这是因为 DNS 被污染后，无法正确解析到 Vercel 服务器的 IP 地址。

然而，当你通过配置了域名 abc.baidu.com 并访问它时，请求会首先发送到 Baidu 的 DNS 服务器。由于 Baidu 的 DNS 服务器并未受到污染，它能够正常解析到你配置的 CNAME 记录 cname.vercel-dns.com.，然后将请求转发到 Vercel 的 DNS 服务器。

Vercel 的 DNS 服务器收到请求后，会解析到正确的目标地址（例如 abc.vercel.com），然后将请求转发到 Vercel 的服务器。因为这个转发过程是在 DNS 服务器层级上进行的，所以即使 vercel.com 的 DNS 被污染，你仍然能够通过 abc.baidu.com 访问到 Vercel 的资源，因为请求的 DNS 解析是在 Baidu 的 DNS 服务器上进行的。

这种方式实际上是通过配置一个中间域名（abc.baidu.com），将请求从一个正常运行的 DNS 服务器转发到另一个受污染的域名的 DNS 服务器，然后再将请求路由到正确的目标地址。这样可以绕过污染的 DNS 解析，确保请求能够正确到达 Vercel 的服务器。

需要注意的是，这种方式只是一种临时解决方案，目的是为了绕过 DNS 污染问题。如果 vercel.com 的 DNS 污染问题得到解决，直接访问 abc.vercel.com 也应该能够正常工作。

#### 请求过程

1. 用户在浏览器中输入 abc.baidu.com。
2. 操作系统的网络设置向配置的 DNS 服务器发送 DNS 查询请求，以获取 abc.baidu.com 的 IP 地址。
3. DNS 服务器接收到查询请求后，在自己的记录中查找 abc.baidu.com 的 DNS 记录。
4. 如果找到了 abc.baidu.com 的 CNAME 记录，并且目标地址是 cname.vercel-dns.com.，则 DNS 服务器会将请求转发到 Vercel 的 DNS 服务器。
5. Vercel 的 DNS 服务器接收到请求后，根据其记录将请求转发到正确的目标地址（例如 abc.vercel.com）。
6. 用户的操作系统接收到目标地址后，通过网络路由将请求发送到对应的服务器，这里是 Vercel 的服务器。
7. Vercel 的服务器接收到请求，并返回与该请求对应的内容。
8. 用户的浏览器接收到服务器返回的内容，并将其显示给用户。

#### SNI 和 DNS 污染

SNI（Server Name Indication）和 DNS（Domain Name System）污染是与网络通信和域名解析相关的两个概念。

- SNI（Server Name Indication）：SNI 是一种扩展协议，用于在进行 TLS（Transport Layer Security）握手时，向服务器指示客户端要访问的域名。在使用 SNI 之前，TLS 握手只能根据 IP 地址来确定要访问的服务器，而无法区分多个共享同一 IP 地址的域名。通过 SNI，客户端可以在握手过程中发送目标域名信息，服务器则可以根据域名来选择合适的证书和配置。

- DNS 污染：DNS 污染是指在 DNS 查询过程中，恶意修改或篡改 DNS 响应的行为。这可能导致域名解析出现错误的结果或无法解析到正确的 IP 地址。DNS 污染可能是由于恶意攻击、网络干扰、劫持或错误的 DNS 配置引起的。当 DNS 污染发生时，用户可能无法访问正确的网站，或者被重定向到恶意网站。

这两个概念在以下情况下可能会有关联：

当 DNS 污染发生时，域名解析的结果可能被修改，导致客户端无法正确解析到目标服务器的 IP 地址。这可能会导致 SNI 握手过程中出现问题，因为客户端无法正确识别服务器所使用的域名。这可能导致 TLS 握手失败、证书验证错误或无法建立安全连接。

因此，DNS 污染可能会对 SNI 握手过程和安全连接产生影响。在这种情况下，使用已被污染的 DNS 服务器进行域名解析可能会导致与服务器的通信问题。

为了解决这些问题，你可以尝试使用可靠的、未被污染的 DNS 服务器进行域名解析，或者使用 VPN（Virtual Private Network）等工具来保护网络通信的安全性和可靠性。
