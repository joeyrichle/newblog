---
title: Hugo装修小记
description: 
slug: modify-record
date: 2026-02-12
image: 
categories: 

tags: 
    - 建站装修
    - Hugo
---

大体建好后，自己又做了一些零零碎碎的修改。

## 样式

### 修改标题字体

我挺喜欢matters的标题字体，截图给Gemini查了下字体，自己又去Adobe官网看了看，决定把标题改成了**思源宋体**，这个字体说是Hugo也支持。修改也简单，直接在`assets/scss/custom.scss`文件里加下列代码：
```scss
//更改标题字体为思源宋体
@import url('https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@700&display=swap');
.article-details .article-title {
    font-family: "Noto Serif SC", "Noto Serif CJK SC", serif;
    font-weight: 700;
    line-height: 1.5;
}
```
第一句@improt是告诉Hugo引用哪里的字体。  
article-details是定义使用的范围，接下来就是字体的名称，字体粗细和行高。数字可以自己调试，找个舒服的。

### 去掉TOC的数字

我主要写随笔居多，数字感觉不搭噶，而且占位长，所以把这个关掉了。
`config/markup.toml`里找到[tableOfContents]这行，把ordered改为false即可。
改完看自动显示变成了圆点，这样我觉得也可以，就没有再弄。
>` ordered    = false`

### 取消文章页面的脚注全部大写
自动显示的英文部分全部是大写，想改成普通状态。  
```scss
//assets/scss/custom.scss中添加下列代码
//取消文章脚注英文全部大写
.article-copyright span {
    text-transform: none;
}
```
