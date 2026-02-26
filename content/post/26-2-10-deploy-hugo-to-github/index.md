---
title: Hugo stack博客建站记录
description: 用Hugo-stack主题在GitHub部署blog并配置自定义域名
slug: deploy-hugo-to-github
date: 2026-02-10
lastmod: 2026-02-11
image: 39.jpeg
categories:

tags:
    - 建站装修
    - Hugo
---


这篇记录一下这个网站的搭建过程。~无他，唯copypaste尔。~  
之后可能会再写一篇建站前的各种试错阶段，会是碎碎念。

首先当然是注册Hugo和GitHub，此外还有一些隐性学习成本，我也还在摸索，就不多写了，有意的就多搜教程和视频看吧。

## 使用Hugo-stack-starter在Github搭建博客网站

非常幸运的是，这个主题的作者Jimmy刚好在我建站的前一天更新并上传了一个视频教程。
按照作者[视频](https://www.youtube.com/watch?v=8qDdQQ6Ifxo)的步骤来，用Hugo-theme-starter做template，在自己的GitHub新建一个repository，基本就是一键到位了。  

**注意Configuration这里选择public，GitHub非pro账户只有选择public之后才能在pages页面看到github actions的选项。**   

等codespaces自动把文件都复制到repo，然后修改`config.toml`里的地址和blog名字。  
自己GitHub名字的博客网站就出来了。

## Modify

把theme-stack里的`assets`和`layouts`这两个文件整个复制到自己的repo主目录里，按照自己的需要修改相关的文件。

为什么全部复制，只复制需要的文件也是没问题的，因为本地没有的话Hugo会自动扫描回源文件。但全部复制的好处是改的时候不需要再单独分别复制挪动，省力，直接在自己的repo里定位文件就可以（**此处感谢友邻Allison，她一指点，如有神助，改起来如虎添翼，比心**）。

modify大部分是参考友邻白石京的[建站装修相关文章](https://thirdshire.com/hugo-stack-renovation/)，个别细节根据需要和心情（主要是代码一多我就懒得弄）有微调。
改完后的理解是，layouts是管页面内容设置的，assets是管风格样式的，所以要加东西改东西基本是这两个文件夹。

### 参考白石京的部分

- 添加Twikoo评论区
  
添加的过程中因为版本不同界面设计有变化所以有点迷惑，随便搜到这篇[教程](https://blog.kevinchu.top/2023/09/19/vercel-deploy-twikoo/)，同时参考视频和twikoo的[快速上手说明](https://twikoo.js.org/mongodb-atlas.html)顺利设置成功。其实twikoo的上手说明写的挺好，仔细看跟着来就可以。

另外谷歌的passkey需要保持开启二次验证，我关了二次验证之后测试twikoo一直失败，于是又重开了。
 
- 圆角标签 

  这里我只用了第一条code，看了下“分类”的效果我觉得也还可以所以没有做其他的改动

- 增加“博客已运行X天X小时X分种“字样
  
  这里我对字体和颜色没有特殊需求，所以风格样式只定义运行时间的部分：
`assets/scss/partials/footer.scss`
```scss
   .running-time {
        color: var(--card-text-color-secondary);
        font-weight: normal;
    }
```

- 外链后显示图标
- 增加返回顶部按钮

- 缩小分类卡片尺寸
`assets/scss/partials/layout/list.scss`,这里我把数字改成了width: 200px;height: 120px;argin-right: 15px

- 增加友链双栏
  
  友链我没有新建页面，而是直接把原有的content/content/page/links文件夹改成friends，只在assets/scss/custom.scss文件添加了双栏样式部分的代码。

```scss
//友情链接双栏
@media (min-width: 1024px) {
    .article-list--compact.links {
        display: grid;
        grid-template-columns: 1fr 1fr;
        background: none;
        box-shadow: none;
        
        article {
            background: var(--card-background);
            border: none;
            box-shadow: var(--shadow-l2);
            margin-bottom: 8px;
            border-radius: 10px;
            &:nth-child(odd) {
                margin-right: 8px;
            }
        }
    }
}
```

- 在归档（博文）列表里显示副标题/简介*
  
  **这个重点写一下**，无脑复制代码commit之后显示的页面是这样的

![](1.png)

搜了下是典型的redundant render（冗余渲染），简单说就是代码重复了。

在layouts/_partials/article-list/compact.html里添加代码时，只需要添加这部分代码到article-title的 </h2>之后。

```html
            {{ with .Params.description }}
            <div class="article-subtitle">
                {{ . }}
            </div>
            {{ end }}
```
放张actions的图更直观：

![](2.png)

- macOS 风格的代码块 (三个圆点我这里没有显示，之后看心情弄)

### 自己摸索和Gemini做的修改和一些tips：

#### 删除右边侧栏widget里的icon
在layouts/_partials/widget里找到`archives.html`，`categories.html`，`tag-cloud.html`，`toc.html`这几个文件，删除里面两个`<div>`之间的代码，icon_name是对应的文件名。

```html
 <div class="widget-icon">
    {{ partial "helper/icon" "icon_name" }}
 </div>
 ```

因为删了icon，也就不需要定义icon样式。  
删除assets/scss/partials/widgets.scss中的这部分代码。
**注意{}的范围**
```scss
.widget-icon {
    svg {
        width: 32px;
        height: 32px;
        stroke-width: 1.6;
        color: var(--body-text-color);
        }
}
```
#### 保留默认语言为英语，开启CJKL确保中文被计数
我想保留网站的默认语言为英语，但用中文写作。为了让wordcount和阅读时间正确显示，仅在congfig.toml里修改
`hasCJKLanguage = true`

## 配置自定义域名（custom domain）

配置域名我是看的这个[视频](https://www.youtube.com/watch?v=_QSr2_pxIJs)的最后部分。  
前面也是教如何deploy Hugo 到GitHub pages的，用的是papermud的主题建到本地。讲得很仔细到位，里面有很多基础知识，包括Hugo怎么装都提到了，非常有收获。他动作很快，很多细节一闪而过，我之前反反复复看了好多遍。

### 买域名

我直接去白石京提到的[spaceship](https://www.spaceship.com)买了域名，买的时候搜一下promo code，能省一点是一点。

上面的视频里还提到另两个网站，[Namecheap](https://www.namecheap.com/domains/)和[GoDaddy](https://jp.godaddy.com) 。
可能不同地域价格会有有变，这里不做推荐，建议比比价。

### GitHub和DNS配置

域名买好后，在repo/settings/pages的Custom domain里填入自己的域名。github会自动进行check。但这时check会显示失败，因为域名网站那边还没有设置将域名链接到GitHub。

回到购买域名的网站，配置DNS。
spaceship是在Launchpad，找到Advanced DNS，按照GitHub docs的[说明](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain)，添加4个A record和1个CNAME record。A record的Host处填入一个@即可。添加完保存后，等待链接成功，会需要一点时间。

![](3.png)

5个record显示都成功链接到GitHub后，返回GitHub pages，custom domain下面的check显示成功后自定义域名就配置完成了。

## 感谢

最后，非常感谢作者Jimmy，友邻Allison，白石京，椒盐豆豉，不然我这个拖延症狂魔不知何年何月才能建成这个网站。

## 还想改的地方

还有一些想改的地方，以后慢慢弄。
- 每篇文章后加emoji表情按钮
- 邮箱订阅
- Twikoo添加动态表情包
- 热力图也想加
- 随机入口
- 微调字号字体

前提都是写得多的话，哈哈。当然如果有人来评论~催促~会更有动力吧。