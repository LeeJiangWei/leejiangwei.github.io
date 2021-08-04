---
title: 干掉Disqus评论区广告
date: 2021-07-20 09:12:04
excerpt: 三天之内删了你，DOM节点都给你扬咯
categories:
    - 博客建设
index_img: https://i.loli.net/2021/07/20/y9aWrPUZBbsvX5u.png
---

# 干掉Disqus评论区广告

这 Disqus 评论区的广告真是越来越离谱了，虽然内容看着还行，但是评论上面六个大图下面也六个大图，都™要比我博客本身的内容还多了，不治治它它就真不知道什么是黑手。

![我啪的一下就点进去看了，很快啊](https://i.loli.net/2021/07/20/ATIczgZXp7SwLPf.png)

这些广告会延迟加载，直接使用 css 隐藏的方法不好用。一般这个 ID 为 disqus_thread 的 div 通常有 2 到 3 个 iframe，一般第一个和第三个都是广告。这个函数会等待两秒，等广告加载出来后再把它隐藏掉。

```javascript
const disqus = jQuery('#disqus_thread');

disqus.ready(function() {
  setTimeout(function() {
    if (disqus.children().length >= 2) {
      const comments = disqus.find('iframe:nth-child(2)').detach();
      disqus.empty().append(comments);
    }
  }, 2000);
});
```

最后将这段脚本插入到自定义 js 中。对于我的 Fluid 主题，把 js 文件放在 source 文件夹下的任意地方，然后在配置文件中指定就好了。
