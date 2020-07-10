# CSS 面试题



1. 什么是 HTML ?

   HyperText Markup Language - 超文本标记语言

2. 什么是 CSS ?

   Cascading Style Sheets - 层叠样式表

3. 什么是文档流 ?

   HTML 内的元素按照默认规则进行布局，比如块级元素从上到下垂直排列，行内元素从左到右水平排列

4. 什么是响应式布局 ? 有哪些实现方案

   就是一个页面能适配在不同的终端上。

   实现方案：1. 媒体查询 2. 使用 rem 单位

5. HTML 中 meta 有哪些 ?

   ```html
   <meta name="viewport" content="width=device-width,initial-scala=1,maximum-scala=1" />
   <meta name="keywords" content="" />
   <meta name="description" content="" />
   <meta charset="UTF-8" />
   ```

6. REM 是什么 ? 怎么实现响应式布局 ?

   是根元素 HTML 的 font-size 值，比如根元素 font-size = 16px = 1rem。

   算法：设计稿尺寸(750) / 预设比例(1rem=100px) = 分辨率尺寸(1980) / ?

7. 什么是盒模型 ? 如何更改盒模型 ?

   盒模型就是元素内容、内边距、边框、外边距

   ```css
   更改盒模型的方法：
   box-sizing: context-box | border-box | inherit
   context-box，默认值，表示将宽高应用到元素的内容上，在宽高之外绘制元素的内边距和边框
   border-box，表示将宽高应用到元素的内容、内边距、边框上
   ```

8. CSS 选择器的优先级 ?

   ```css
   !importan(非选择器)
   内联样式(非选择器)
   id 选择器
   class 选择器 = 属性选择器 = 伪类
   标签选择器 = 关系选择器 =  伪元素
   通配符选择器
   
   属性选择器有哪些：
   a[href] - 只对设置了 href 的 a 标签有作用
   a[href="google.com"] - 只对 a 标签中 href 属性是 google.com 的标签起作用
   img[alt] - 对 img 的 alt 属性起作用
   
   伪类有哪些（死记）：
   a 标签的四种状态，:link / :visited / :hover / :active
   :focus - 有键盘输入焦点的元素可以使用
   :first-child - 作为某个元素中的第一个 x 标签，比如 p:first-child 是作为某个元素中的第一个 p 标签
   
   伪元素有哪些：
   ::before、::after
   ::first-line - 向文本的首行添加特殊样式
   ::first-letter - 向文本的第一个字母添加特殊样式
   ```

9. 定位的方式有哪些，哪些脱离了文档流 ?

   `static、relative、absolute、fixed`，其中 `absolute、fixed` 脱离了文档流

10. 块级元素和行内元素

   块级：`div 、form、h1-h6、hr、ol、ul、table、p`

   行内：`a、span、img、i、input、label、select、textarea、b、em`

11. 清除浮动有哪些方法

    1. 给父元素设置高度
    2. 在浮动元素下方放一个块级元素来清除浮动的影响，一般是用伪元素 ::after，给他加上 clear: both，也可以使用其他块级元素
    3. `<br clear="both" />` 或者浮动元素下方的任意元素 style="clear: both" 包括没有高度的元素
    4. 给父元素设置 overflow: hidden; 或者 overflow: auto; 把父元素变成 BFC

12. 单行超出文本省略 ?

    ```css
    overflow: hidden;
    text-overflow:ellipsis;
    white-space: nowrap;
    ```

13. 多行超出文本省略 ?

    ```css
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: 3;
    overflow: hidden;
    
    // JS 控制，写个公共方法，超出多少文字就 ...
    
    ```

14. 使用 Flex 布局：20 个元素，一排三个，两端对齐，每个元素等宽 ?

    ```css
    Flex，水平的主轴，垂直的交叉轴
    作用于容器的属性：
    flex-direction: row | row-reverse | column | column-reverse
    flex-wrap(一条轴线放不下该怎么办): no-wrap | wrap | wrap-reverse
    flex-flow(上面两个的简写): row no-wrap
    justify-context(主轴的对齐方式): flex-start | flex-end | center | space-between | space-around
    align-items(交叉轴对齐方式): flex-start | flex-end | center | baseline | 默认值(占满容器高度)
    align-context(多轴线的对齐方式): flex-start | flex-end | center | space-between | space-around
    作用于项目的属性：
    order: 定义项目的排列顺序，越小越靠前，默认 0，可以为负数
    flex-grow: 定义项目的放大比例，默认 0。如果所有项目此值为 1，则平分空间；值为 2，则多占据一倍的空间
    flex-shirnk: 定义项目的缩小比例，默认 1。如果所有项目此值为 1，则都将等比例缩小；值为 0，则不变
    flex-basis: 定义项目在分配多余空间之前，占据的主轴空间，默认 auto
    flex: 是以上三个的简写，默认 0 1 auto
    align-self: 允许项目有自己的对齐方式，会覆盖 align-items，默认 auto，表示继承父级，没有父级就会占满容器高度
    ```

15. 如何做到图片不变形 ?

   1. 设置背景图，而不是 img
   2. 上传图片时控制好宽高
   3. 使用 object-fit 属性，IE 不支持，移动端都可以
      1. object-fit: contain(保持原有宽高比适应 img) | cover(如果内容超出会被裁剪来适应 img) | fill(会被拉伸来适应 img)

16. 元素水平垂直居中的方法有哪些 ?

    ```css
    行内元素水平居中：text-align:center;
    行内文字垂直居中：line-height: height;
    多行文字垂直居中：父级设置 display: table-cell; vertical-align:middle;
    单一块级水平居中：margin: auto;
    多个块级水平居中：父级设置 text-align: center; 子级设置 display: inline-block;
    
    已知自身宽高(width: 100px; height: 100px;)
    1. 绝对定位
    position: absolute;
    top: 0; left: 0; bottom: 0; right: 0; margin: auto;
    2. 绝对定位 + margin 负值拉回自身宽高的一半
    position: absolute;
    top: 50%; left: 50%; margin: -50px 0 0 -50px;
    
    未知宽高：
    1. 如果是 inline 或 inline-block
    display: table-cell;
    text-align: center;
    vertical-align: middle;
    2. CSS3 transform
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    3. flex
    display: flex;
    justify-content: center;
    align-items: center;
    ```

17. CSS 动画

    定义一个 @keyframes xxx {keyframes selector: style}，再通过 animation: keyframes name 2s infinite;

18. 移动端开发如何适配 ?

    和响应式布局是一样的。

19. 什么是 CSS Hacks ?

    解决浏览器兼容性问题的技术。

    比如 flex 兼容 webkit 内核浏览器，需要加上 display: -webkit-flex;

20. 如何解决 a 标签在访问后 hover 样式失效的问题？

    ```css
    // a 标签的样式优先级顺序是 link -> visited -> hover -> active
    
    // 正确写法是
    a:visited { ... }
    a:hover { ... }
    ```

21. 如何理解 z-index

    元素的堆叠顺序

22. 什么是 BFC

    块级格式化上下文。BFC 的触发条件有：

    ```
    1. HTML 根元素
    2. position: 绝对定位和固定定位
    3. float: left | right
    4. display: inline-block | flex | table-cell
    5. overflow: hidden | scroll | auto
    ```

    BFC 能解决哪些问题：

    1. 可以解决两个 DIV 之间外边距塌陷问题
    2. 浮动元素会导致父级元素没有高度，这时可以使用 overflow: hidden 来将父级元素变成一个 BFC
    3. 浮动元素脱离文档流，会覆盖普通元素，可以通过将普通元素变成 BFC 来解决这个问题

    ```
    其他格式上下文：
    1. IFC（内联格式上下文）
    2. FFC（网格布局格式上下文）
    3. GFC（自适应格式上下文）
    ```

23. link 和 @import 的区别

    1. link 是 HTML 标签，@import 属于 CSS 范畴
    2. link 在页面加载时载入，@import 在页面完全加载后才会载入

24. 视口是什么

    ```css
    视口分为三种
    布局视口(Layout Viewport)，通过 document.documentElement.clientWidth 获取
    视觉视口(Visual Viewport)，是用户当前看到的区域，可以通过缩放来操作视口，又不影响布局视口
    理想视口(Ideal Viewport)，是浏览器厂商引入的概念，它就是屏幕分辨率的值，对应的像素叫设备逻辑像素(DIP)，如果不缩放，那么 CSS 中的 1px 就等于 1 DIP
    我们通常用 HTML 元标签来设置布局视口为屏幕的分辨率，同时设置缩放比例为 1，以及禁止缩放。
    ```

25. 一倍图，二倍图，三倍图，分别是什么

    ```
    首先，设备逻辑像素 * 设备像素比 = 设备物理像素（设备像素比可以通过 window.devicePixelRatio 来获取，CSS 也可）
    常见的设备比有：
    普通屏幕：devicePixelRatio = 1
    视网膜屏：devicePixelRatio = 2
    手机屏幕：devicePixelRatio = 2 or 3
    ```

26. em/rem/px 分别是什么，有什么区别

    em 相对长度单位，表示元素 `font-size` 的计算值它是相对父元素而言的。浏览器的默认字体高都是 16px，未经调整的浏览器显示 1em = 16px

    rem 相对长度单位，代表根元素 `<html>` 的 `font-size` 大小。

    一般通过 JS 来获取文档宽度，然后通过以下公式设置文档的基础字体大小为文档宽度的 1/100：

    `document.documentElement.style.fontSize = document.documentElement.clientWidth / 100 + 'px'`

    这样，就相当于是把文档宽度划分为 100 份，1rem 就相当于是 1 份的宽度，假设设计图是 640px，

27. 移动端如何处理 1px

    ```css
    1. 伪元素，设置 transform 缩放，支持边框颜色和圆角，兼容性好，但是影响利用伪元素清除浮动
    .px::after {
      display: inline-block;
      content: '';
      position: absolute;
      width: 200%; // transform 缩小了，因此这里要放大，高度同理
      height: 200%;
      border: 1px solid;
      transform: scale(0.5); // 线条缩小一倍
      transform-origin: top left; // 设置元素的基点，全局缩小基点会变成父级中心，这里要移回去
      box-sizing: border-box; // 伪元素带了边框，宽高会 +1，这里要改变盒模型
    }
    
    2. 阴影，box-shadow，支持边框颜色和圆角，兼容性好，视觉可查
    3. 图片，差评
    4. viewport 缩放，获取浏览器环境的设备像素比，然后根据不同的比例，设置不同的缩放，影响较大，未知问题较多
    ```

28. 如何实现两列布局（左侧宽度固定，右侧自适应）

    ```css
    // 1. table、table-cell
    1. 父元素设置 display: table;
    2. 右侧子元素 display: table-cell;
    
    // 2. flex
    1. 父元素设置 display: flex;
    2. 左侧子元素固定大小，右侧子元素 flex: 1;
    ```

29. 如何实现三列布局

    ```css
    // 1. table、table-cell
    1. 父元素设置 display: table;
    2. 两边子元素固定宽度，分别左右浮动，中间子元素 display: table-cell; width: 100%;
    
    // 2. flex
    1. 父元素设置 display: flex;
    2. 两边子元素固定宽度，中间子元素 flex: 1;
    ```

30. 如何实现一行平分

    ```css
    // 1. table、table-cell
    1. 父元素设置 display: table;
    2. 子元素设置 display: table-cell
    
    // 2. flex
    1. 父元素设置 display: flex;
    2. 子元素设置 flex: 1;
    ```

