---
layout: post
title: "记性好不好都需要烂笔头"
date: 2018-04-20
tag: Extra
---

#### ———————————— 写在开始 ————————————————————————

记录工作中遇到的问题，可能大部分是CSS方面的，以后再碰到可以直接复制粘贴，希望这篇绵绵长 (๑•̀ㅂ•́)و✧

#### ———————————— 烂笔头 —————————————————————————

#### 1. overflow:hidden & border-radius 隐藏失效

父元素 overflow:hidden; border-radius，子元素设置 transform 后，超出部分无法隐藏

参考：[Webkit overflow issue on border-radius and transform](https://stackoverflow.com/questions/27296900/webkit-overflow-issue-on-border-radius-and-transform)

	-webkit-mask-image: url(data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAIAAACQd1PeAAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJbWFnZVJlYWR5ccllPAAAAA5JREFUeNpiYGBgAAgwAAAEAAGbA+oJAAAAAElFTkSuQmCC);
