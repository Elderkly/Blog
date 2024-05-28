## CSS

### flex布局
容器属性：
- flex-direction: 主轴方向
  - row：主轴为水平方向，子项从左到右排列。
  - row-reverse：主轴为水平方向，子项从右到左排列。
  - column：主轴为垂直方向，子项从上到下排列。
  - column-reverse：主轴为垂直方向，子项从下到上排列。

- flex-wrap: 如何换行
  - nowrap：不换行（默认值）。
  - wrap：换行，第一行在上方。
  - wrap-reverse：换行，第一行在下方。

- flex-flow: flex-direction和flex-wrap的简写形式
  - 默认：row wrap

- justify-content: 主轴上的对齐方式
  - flex-start：左对齐（默认值）。
  - flex-end：右对齐。
  - center：居中对齐。
  - space-between：两端对齐，子项之间的间隔相等。
  - space-around：每个子项两侧的间隔相等。

- align-items: 副轴对齐方式
  - stretch：拉伸以适应容器（默认值）。
  - flex-start：交叉轴的起点对齐。
  - flex-end：交叉轴的终点对齐。
  - center：交叉轴的中点对齐。
  - baseline：第一行文字的基线对齐。

- align-content: 用来定义多根轴线的对齐方式。如果只有一根轴线，该属性不起作用
  - flex-start：与交叉轴的起点对齐。
  - flex-end：与交叉轴的终点对齐。
  - center：与交叉轴的中点对齐。
  - space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
  - space-around：每根轴线两侧的间隔都相等。
  - stretch：轴线将占满整个交叉轴。

项目属性:
- order: 用来定义子项的排列顺序。默认值为0，值越小，排列越靠前。
  
- flex-grow: 用来定义子项的放大比例。默认为0，即如果存在剩余空间，也不放大。

- flex-shrink:  用来定义子项的缩小比例。默认为1，即如果空间不足，该项目将缩小。

- flex-basis: 用来定义子项在主轴上的初始大小。可以为具体的长度值（如20%或100px）或auto。    
  
- align-self: 用来允许单个子项有与其他子项不一样的对齐方式，可覆盖align-items属性。
  - auto：继承父元素的align-items属性（默认值）。
  - flex-start：交叉轴起点对齐。
  - flex-end：交叉轴终点对齐。
  - center：交叉轴中点对齐。
  - baseline：第一行文字的基线对齐。
  - stretch：拉伸以适应容器。 

- flex: flex-grow和flex-shrink和flex-basis的缩写。

当设置了flex-basis的值，并设置了flex-shrink:0，则width失效

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


### CSS样式优先级
!important > 内联样式 > ID选择器 > 类选择器、属性选择器和伪类选择器 > 元素选择器和伪元素选择器 > 通配选择器、子选择器、相邻选择器   

### 块级元素与行内元素
#### 块级元素
1.占据一整行   
2.可以设置宽度、高度、内外边距等属性，并且可以通过CSS控制其布局行为   
3.<div>、<p>、<h1>到<h6>、<ul>、<li>     

#### 行内元素
1.通常不会独占一行，而是根据内容自动换行   
2.宽度由内容决定，无法设置固定的宽度    
3.<span>、<a>、<strong>、<em>、<b>、<i>  

### 父元素宽高不定 却对居中
```css
/* flex */
display: flex;
justify-content: center; /* 水平居中 */
align-items: center; /* 垂直居中 */

/* 绝对定位 */
position: absolute;
top: 50%;
left: 50%;
transform: translate(-50%, -50%);
```