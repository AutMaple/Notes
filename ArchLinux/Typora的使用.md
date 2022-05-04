# 使用 typora 画流程图

## 什么是 Mermaid

Mermaid 是一种基于 Javascript 的绘图工具，使用类似于 Markdown 的语法，使用户可以方便快捷地通过代码创建图表。[Mermaid项目地址](https://github.com/mermaid-js/mermaid)

## 怎么使用 Mermaid

1. 使用 Mermaid 渲染器
2. 使用集成了 Mermaid 渲染器的编辑器，如 typora。使用时，需要将代码块的语言选择为Mermaid。

## Mermaid 能够画哪些图

- 饼状图：使用 `pie` 关键字，具体用法后文将详细介绍
- 流程图：使用 `graph` 关键字，具体用法后文将详细介绍
- 序列图：使用 `sequenceDiagram` 关键字
- 甘特图：使用 `gantt` 关键字
- 类图：使用 `classDiagram `关键字
- 状态图：使用 `stateDiagram` 关键字
- 用户旅程图：使用 `journey` 关键字

## 饼状图

```mermaid
pie
    title 为什么总是宅在家里？
    "喜欢宅" : 15
    "天气太热或太冷" : 20
    "穷" : 500
```

## 流程图

```mermaid
graph LR
    A[Start] --> B{Is it?};
    B -- Yes --> C[OK];
    C --> D[Rethink];
    D --> B;
    B -- No ----> E[End];
```

### 流程图方向

用于开头，声明流程图的方向：

- `graph` 或 `graph TB` 或`graph TD`：从上往下
- `graph BT`：从下往上
- `graph LR`：从左往右
- `graph RL`：从右往左

### 流程图图形

```mermaid
graph
    默认方形
    id1[方形]
    id2(圆边矩形)
    id3([体育场形])
    id4[[子程序形]]
    id5[(圆柱形)]
    id6((圆形))

```

```mermaid
graph
	id1{菱形}
	id2{{六角形}}
	id3[/平行四边形/]
	id4[\反向平行四边形\]
	id5[/梯形\]
	id6[\反向梯形/]
```

### 流程图连线样式

#### 实线箭头

```mermaid
graph LR
a-->b--文本1-->c-->|文本2|d
```

#### 粗实线箭头

```mermaid
graph LR
a==>b==文本==>c
```

#### 虚线箭头

```mermaid
graph LR
a-.->b-.文本.->c
```

#### 无箭头线

```mermaid
graph LR
a---b
b--文本1!---c
c---|文本2|d
d===e
e==文本3===f
f-.-g
g-.文本.-h
```

#### 其他箭头

```mermaid
flowchart LR
    A o--o B
    B <--> C
    C x--x D
    
    旧连线 --文本--> 也会不同
```

### 连线方式

直连

```mermaid
graph LR
   A -- text --> B -- text2 --> C
```

多重链

```mermaid
graph 
   a --> b & c--> d
   
   A & B--> C & D
   
    X --> M
    X --> N
    Y --> M
    Y --> N
```

## 其他

子图：需要将 `graph`关键字改为 `flowchart`，在代码段的开始加入 `subgraph`，尾部加入 `end`

```mermaid
flowchart TB
    c1-->a2
    subgraph one
    a1-->a2
    end
    subgraph two
    b1-->b2
    end
    subgraph three
    c1-->c2
    end
    one --> two
    three --> two
    two --> c2
```

注释：在行首加入`%%`即可。

```mermaid
graph LR
%%这是一条注释，在渲染图中不可见
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
```

