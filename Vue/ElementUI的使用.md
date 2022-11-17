# el-scrollbar 的使用

el-scrollbar 组件渲染之后的结构

```html
<div class="el-scrollbar"> //根元素，包裹所有元素
    <div class="el-scrollbar__wrap"> // wrap 元素，是视觉视口元素，它代表着元素最终展示的窗口大小
        <div class="el-scrollbar__view"> // 布局视口元素
            // put the content here
        </div>
    </div>
</div>
```

el-scrollbar 接收几个属性：

```javascript
props: {
  native: Boolean,
  wrapStyle: {}, // 外层盒子样式
  wrapClass: {}, // 外层盒子 class
  viewClass: {}, // 内层盒子 class
  viewStyle: {}, // 内层盒子 style
  noresize: Boolean, // 如果 container 尺寸不会发生变化，最好设置它, 它可以优化性能
  tag: {
    // 渲染时所用的标签，默认为 div
    type: String,
    default: "div",
  },
},
```

el-scrollbar 在使用的时候必须指定高度，通常可以将 el-scrollbar 放在一个 div 中，然后设置 div 的高度，然后设置 el-scrollbar 的高度为 100%.

```html
<div style="height: 100vh;">
    <el-scrollbar style="height: 100%;"></el-scrollbar>
</div>
```

## 控制 el-scrollbar 的滚动条

要控制 el-scrollbar 组件的滚动条，需要使用 vue 中的 ref 属性

```html
<el-scrollbar ref="scrollbar"></el-scrollbar>
```

```javascript
this.$refs.scrollbar.wrap.scrollTop
```

通过 scrollTop 属性来控制滚动条
