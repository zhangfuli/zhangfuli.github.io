---
title: letax中文问题
date: 2018-04-20 17:37:41
tags: 生活
categories: 生活
---



#### 写在前面
###### 最近一直在忙着申请实习生、申请夏令营，简历肯定是必不可少的，latex做pdf简历还是很不错的，

<a href="https://www.sharelatex.com/project">sharelatex</a>
###### 反正是觉得比在word里写好然后生成pdf高大上一点，毕竟还是喜欢敲代码…

###### 但是存在着输入中文无法识别的问题，英文大佬走开…下面是解决方案
#### 解决方案
    \usepackage{CJKutf8}
    \begin{document}
    \begin{CJK*}{UTF8}{gbsn}
    ....
    ....
    ....
    \end{CJK*}
    \end{document}
###### 这不就是导入个包嘛？对这就是导入个包完美解决

