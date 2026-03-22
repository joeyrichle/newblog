---
title: Hugo Stack装修小记
description: 
slug: modify-record
date: 2026-02-13T09:00:00+09:00
lastmod: 2026-03-22T13:40:00+09:00
image: 
categories: 

tags: 
    - 建站装修
    - Hugo
---

博客大体建好后，自己又做了一些零零碎碎的修改。

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

## 内容
### 给每篇文章添加字数统计

因为已经有了阅读时间，想把字数统计放在一起。

layouts\partials\article\components\details.html里找到 footer class="article-time" 板块，直接在{{ if $showReadingTime }}部分插入wordcount的line，没有单独写。全部显示如下：

```html
<footer class="article-time">
     {{ if $showDate }}
            <div>
                {{ partial "helper/icon" "date" }}
                <time class="article-time--published">
                    {{- .Date | time.Format (or .Site.Params.dateFormat.published "Jan 02, 2006") -}}
                </time>
            </div>
    {{ end }}

    {{ if $showReadingTime }}
        <div>
            {{ partial "helper/icon" "clock" }}
            <span class="article-time--reading">
            <!-- 在此插入以下代码 -->    
                 {{ T "article.wordCount" .WordCount  }}
            <!-- 在此结束 -->    
                {{ T "article.readingTime" .ReadingTime }}
            </span>
        </div>
    {{ end }}
</footer>
{{ end }}
```

同时要把stack主题的i18n文件夹加到主目录下，我的默认语言是英语，所以只复制了en.toml，如果是中文就复制zh.toml。
在`[article.readingTime]`下面加入字句。
```toml
[article.readingTime]
        one   = "{{ .Count }} min read"
        other = "{{ .Count }} min read"
//wordcount
    [article.wordCount]
        one   = "{{ .Count }} word"
        other = "{{ .Count }} words"
```
理论上不会有一个字的文章，所以只写other这句就可以。  
如果是中文，可以写 `other = "本文共计{{ .Count }} 字"`

这样出来的效果是 3270words，7 minutes read。我更想看千字统计，所以进一步调整成下面这样⬇，以及调整了图标和字之间的间距。

千字统计的code
```html
 {{ if $showReadingTime }}
//缩小图标和文字之间的空隙，具体调整gap后的数字 
    <div style="display: flex; align-items: center; gap: 0.7rem;">
        {{ partial "helper/icon" "clock" }}
         <span class="article-time--reading">
        //千字统计部分代码   
            {{- if ge .WordCount 1000 -}}
                About {{ printf "%.2fK" (div .WordCount 1000.0) }} words
            {{- else -}}
                About {{ .WordCount }} word
             {{- end -}}, 
        <!-- 在此结束 -->        
            {{ T "article.readingTime" .ReadingTime }}
        </span>
    </div>
{{ end }}
```

注意⚠️ 这里我没有动en.toml里的句子，而是把文字About和word加在了detail里。推荐hugo server多运行几次看效果。有时需要ctrl+c关掉重新运行，电脑也需要nap。显示效果如下：
![](53.png)