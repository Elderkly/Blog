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

### flex

在一个 flex 布局中，设置 flex:1,min-width: 0 ，保证内容不超出外层容器  
** 如果没有设置 min-width，当内容大于剩余盒子宽度时会超出父盒子，设置 min-width 保证内容局限在父盒子内。**

### position: sticky

吸顶效果，需设置 top

### 渐变背景色加入动画

**https://stackoverflow.com/a/51362339**

## Windi CSS

### Tip

1.设置 rotate 或是其他变换属性需先设置 transform  
2.子组件设置 group-hover 需在父组件设置 group  
3.镂空字体：`text-stroke-1 text-stroke-dark-500 text-transparent`  
若字体有重叠设置属性`fontFamily: "'Inter', sans-serif" `
