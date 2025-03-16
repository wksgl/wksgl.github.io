---
title: LaTex渲染问题
date: 2025-03-15 14:10:06
tags:
  -疑难杂症
mathjax: true
---
:::tips
关于LaTex在hexo上的渲染问题
:::
<!--more-->
## 引言
笔者在记录Markdown学习笔记时发现，在本地预览正常显示的LaTex公式上传到博客时渲染出错。原因是Hexo 默认搭配的 Markdown 渲染器是 **hexo-renderer-marked**，但其支持的渲染格式有限，不利于自由写作。  
经过查找资料发现 **hexo-renderer-markdown-it** 渲染器能够解决这个问题，并提供更多插件帮助用户更优雅地进行写作

### 渲染器安装
1. #### 首先卸载原有渲染器:

`
npm un hexo-renderer-marked --save
`

2. #### 安装新的渲染器:
`npm i hexo-renderer-markdown-it --save
`

### 文件配置及插件安装

1. #### 找到Hexo的配置文件_config.yml，并将以下代码添加到配置文件底部:

```yaml
markdown:
  preset: "default"
  render:
    html: true
    xhtmlOut: false
    langPrefix: "language-"
    breaks: true
    linkify: true
    typographer: true
    quotes: "“”‘’"
  enable_rules:
  disable_rules:
  plugins:
    - markdown-it-abbr
    - markdown-it-cjk-breaks
    - markdown-it-deflist
    - markdown-it-emoji
    - markdown-it-footnote
    - markdown-it-ins
    - markdown-it-mark
    - markdown-it-sub
    - markdown-it-sup
    - markdown-it-checkbox
    - markdown-it-imsize
    - markdown-it-expandable
    - name: markdown-it-container
      options: success
    - name: markdown-it-container
      options: tips
    - name: markdown-it-container
      options: warning
    - name: markdown-it-container
      options: danger
  anchors:
    level: 2
    collisionSuffix: ""
    permalink: false
    permalinkClass: "header-anchor"
    permalinkSide: "left"
    permalinkSymbol: "¶"
    case: 0
    separator: "-"
```

2. #### 插件安装及使用
   1.  ##### 使用如下命令安装3个插件:
    
        ```
        npm i markdown-it-checkbox
        npm i markdown-it-imsize
        npm i markdown-it-expandable
        ```
        其他插件渲染器自带，无需安装
   2. ##### markdown-it-imsize:自定义图片宽高
        `![test](image.png =100x200)`

   3. ##### markdown-it-expandable：折叠/展开内容  
      ```
      +++ **点击折叠**  
      这是被隐藏的内容
      +++
      ```
        
      效果:
      +++ **点击折叠**
      这是被隐藏的内容
      +++

   4. ##### markdown-it-container：自定义容器  
      ```:::tips  
      :::tips
      **提示**  
      这是一个提示
      :::

      :::warning
      **注意**
      这是一个警告  
      :::

      ::: danger
      **警告**
      这是一个危险信号
      :::

      ::: success
      **成功**
      这是一个成功信号
      :::  
      ``` 
      效果:
      ::: tips
      **提示**
      这是一个提示
      :::

      ::: warning
      **注意**
      这是一个警告
      :::

      ::: danger
      **警告**
      这是一个危险信号
      :::

      ::: success
      **成功**
      这是一个成功信号
      :::
      自定义容器还需要在 Hexo 的主题下的控制 Markdown 渲染样式文件中配置如下 CSS 内容：（否则不会显示背景色填充效果）:

      ```CSS
      .tips {
      padding-left: 10px;
      background-color rgba(52,152,219,.3);
      border-left 4px solid rgb(52,152,219);
      color: darken(rgb(52,152,219),20%);
      }
      .success {
          padding-left: 10px;
          background-color rgba(46,204,113,.3);
          border-left 4px solid rgb(46,204,113);
          color: darken(rgb(46,204,113),20%);
      }
      .warning {
          padding-left: 10px;
          background-color rgba(241,196,15,.3);
          border-left 4px solid rgb(241,196,15);
          color: darken(rgb(241,196,15),20%);
      }
      .danger {
          padding-left: 10px;
          background-color rgba(231,76,60,.3);
          border-left 4px solid rgb(231,76,60);
          color: darken(rgb(231,76,60),20%);
      }
      ```
更换完渲染器后LaTex公式就能够正常渲染了

### 不更换渲染器的方式
如果不想更换渲染器，也可以通过引入LaTeX自动渲染脚本实现LaTaX公式渲染（MathJax或KaTeX选其一）。
1. **引入MathJax自动渲染js**  
笔者使用的NexT主题需在配置文件取消source/_data/footer.njk注释，在对应路径建立同名文件，最后在footer.njk文件中添加以下代码:
```javascript
<script>
MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']]
  }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>
```
2. **引入KaTeX自动渲染js**
```javascript
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.css" integrity="sha384-GvrOXuhMATgEsSwCs4smul74iXGOixntILdUW9XmUC6+HX0sLNAK3q71HotJqlAn" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/katex.min.js" integrity="sha384-cpW21h6RZv/phavutF+AuVYrr+dA8xD9zs6FwLpaCct6O9ctzYFfFr4dgmgccOTx" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.16.8/dist/contrib/auto-render.min.js" integrity="sha384-+VBxd3r6XgURycqtZ117nYw44OOcIax56Z4dCRWbxyPt0Koah1uHoK0o4+/RRE05" crossorigin="anonymous"></script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        renderMathInElement(document.body, {
          // customised options
          // • auto-render specific keys, e.g.:
          delimiters: [
              {left: '$$', right: '$$', display: true},
              {left: '$', right: '$', display: false},
              {left: '\\(', right: '\\)', display: false},
              {left: '\\[', right: '\\]', display: true}
          ],
          // • rendering keys, e.g.:
          throwOnError : false
        });
    });
</script>
```

### ***注:*** 
Hexo在markdown文章中写LaTeX公式时，有个经典问题就是一些字符会被markdown渲染器误认为是转义符号而被丢失，之后公式引擎再对公式渲染时语义已经改变导致渲染出错，最典型的例子就是换行符`\\`会被转义成\，导致最终渲染的公式内容不换行。本方式会存在这样的问题，我的应对方法是，针对有可能被转义的公式（如带换行符的），用`<span>`或者`<div>`标签将LaTeX公式包裹起来，这样公式内容就不会被markdown渲染器识别为转义字符。注意`<span>`或`<div>`标签应该与markdown内容分隔开，最好空出一行然后写标签，标签独占一行，以免被markdown渲染干扰。

例如，某矩阵公式:
```LaTex
  $$
  \begin{bmatrix}
      a & b \\
      c & d
  \end{bmatrix}
  $$  
```
直接这样写会导致渲染异常:

  $$
  \begin{bmatrix}
      a & b \\
      c & d
  \end{bmatrix}
  $$  

使用`<span>`标签包裹:
```LaTex
<span>
$$
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
$$  
</span>
```

效果:

<span>
$$
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
$$  
</span>

可以发现渲染正常。

   

                   