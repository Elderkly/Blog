## CSS

### display

|   display    | 作用                                                 |
| :----------: | :--------------------------------------------------- |
|     none     | 隐藏                                                 |
|    block     | 块状元素，默认继承父级宽度，独占一行，可设置宽高边距 |
|    inline    | 行内元素，在同一行，不支持设置宽高边距               |
| inline-block | 行内块状元素，在同一行由可以设置宽高边距             |
|   inherit    | 继承父级 display                                     |

### 文本超出处理

```css
/* 超出显示省略号 */
.p1 {
  width: 200px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}
/* 超出两行显示省略号 */
.p2 {
  width: 200px;
  word-break: break-all;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2; /* 这里是超出几行省略 */
  overflow: hidden;
}
```

|     属性      |                    参数                     | 作用                                                                                                                      |
| :-----------: | :-----------------------------------------: | :------------------------------------------------------------------------------------------------------------------------ |
| text-overflow |    clip/ellipsis/string/initial/inherit     | 剪切/用...代替/用特定文本代替/默认值/继承 (需配合 white-space: nowrap;overflow: hidden;使用)                              |
|  white-space  | normal/pre/nowrap/pre-wrap/pre-line/inherit | 空白会被浏览器忽略/空白会被浏览器保留/文本不会换行/保留空白符序列，但是正常地进行换行/合并空白符序列，但是保留换行符/继承 |

### !!!! flex

在一个 flex 布局中，设置 flex:1,min-width: 0 ，保证内容不超出外层容器  
** 如果没有设置 min-width，当内容大于剩余盒子宽度时会超出父盒子，设置 min-width 保证内容局限在父盒子内。**

### position: sticky

吸顶效果，需设置 top

### 渐变背景色加入动画

**https://stackoverflow.com/a/51362339**

### animation

```
/* @keyframes duration | timing-function | delay |
   iteration-count | direction | fill-mode | play-state | name */
animation: 3s ease-in 1s 2 reverse both paused slidein;

/* @keyframes duration | timing-function | delay | name */
animation: 3s linear 1s slidein;

/* @keyframes duration | name */
animation: 3s slidein;
```

## Windi CSS

### Tip

1.设置 rotate 或是其他变换属性需先设置 transform  
2.子组件设置 group-hover 需在父组件设置 group  
3.镂空字体：`text-stroke-1 text-stroke-dark-500 text-transparent`  
若字体有重叠设置属性`fontFamily: "'Inter', sans-serif" `

### 文本首字母大写

`capitalize`

### 背景图

```css
background: url("/static/bg.png");
background-size: 100% 100%;
background-attachment: fixed;
```

### 实现毛玻璃效果

```css
background: rgba(255, 255, 255, 0.2);
backdrop-filter: saturate(180%) blur(20px);
```

### React 禁用 input 滚动

```javascript
<input onWheel={(e: any) => e.target.blur()} />
```

### React 文本超出处理

```javascript
<div
  className="px-6 mt-3 mb-10 text-base text-white overflow-hidden"
  style={{
    wordBreak: "break-all",
    display: "-webkit-box",
    WebkitBoxOrient: "vertical",
    WebkitLineClamp: 10,
  }}
>
  {description}
</div>
```

### TailwindCSS 深色模式

**https://www.jianshu.com/p/0c520ba1862f**

### Flex 布局 子元素超出父元素 父元素宽度被压缩

**https://blog.csdn.net/weixin_42678675/article/details/120952254**

### Flex 主轴居中的情况下 单独改变某个元素的排列

**https://blog.csdn.net/qq846294282/article/details/113698456**

### 无法对同一个元素分别指定不同的 overflow-x 和 overflow-y

**https://blog.csdn.net/wuyou1336/article/details/78965920**

### 英文换行

**https://tailwindcss.com/docs/word-break**
