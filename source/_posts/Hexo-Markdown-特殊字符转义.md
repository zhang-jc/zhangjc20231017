title: Hexo Markdown 特殊字符转义
tags:
  - Hexo
  - Markdown
categories:
  - 语言
  - Markdown
date: 2017-03-30 18:53:52
---

在使用 Markdown 编写 Hexo 的 Blog 时，对于特殊字符使用“\”转义有时会不成功，最好的方式是直接使用特殊字符的编码，对应如下：

<!-- more -->

    ! &#33; — 惊叹号Exclamation mark 
    ” &#34; &quot; 双引号Quotation mark # &#35; — 数字标志Number sign $ &#36; — 美元标志Dollar sign 
    % &#37; — 百分号Percent sign 
    & &#38; &amp; Ampersand 
    ‘ &#39; — 单引号Apostrophe 
    ( &#40; — 小括号左边部分Left parenthesis 
    ) &#41; — 小括号右边部分Right parenthesis 
    * &#42; — 星号Asterisk 
    + &#43; — 加号Plus sign 
    < &#60; &lt; 小于号Less than 
    = &#61; — 等于符号Equals sign 
    > &#62; &gt; 大于号Greater than 
    ? &#63; — 问号Question mark 
    @ &#64; — Commercial at 
    [ &#91; --- 中括号左边部分Left square bracket 
    \ &#92; --- 反斜杠Reverse solidus (backslash) 
    ] &#93; — 中括号右边部分Right square bracket 
    { &#123; — 大括号左边部分Left curly brace 
    | &#124; — 竖线Vertical bar 
    } &#125; — 大括号右边部分Right curly brace 