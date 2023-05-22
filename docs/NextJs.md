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
