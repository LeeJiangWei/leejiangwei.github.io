---
title: 如何制作Unravel和damedane的Deepfake换脸视频
date: 2021-06-16 19:01:35
excerpt: 海猫的痛，你们懂吗
categories: 随笔
index_img: /img/deepfake/pain_of_oceancat.png
---

# 如何制作Unravel和damedane换脸视频
虽然这梗已经火了好久了，但是最近看多了，自己也想试着搞一个。奇怪的是知乎和B站居然搜不到教程，而上外网一下就找到了，于是写一个博客记录一下，顺便帮助有需要的人（虽然应该不会有人看我的博客）。

## 准备照片和视频
### 人脸照片
准备一张人脸照片（受害人），裁剪为长宽比例1：1，人脸最好在中间。这里请出明日方舟的制作人海猫络合物先生。
![Ocean Cat](/img/deepfake/oceancat.png)
### 母体视频
如果是要制作damedane的，就不需要自己准备了，代码里已经预设好了。
Unravel的视频模板在这：[Unravel模板](https://www.kapwing.com/explore/unravel-tokyo-ghoul-meme-template)，直接下载下来一会儿用。
#### 题外话
这个翻唱Unravel的小姐姐YouTube视频在[这里](https://www.youtube.com/watch?v=Dsd9X_7WSd4)，下面的评论都在感谢她提供了这么好的deepfake模板，真是令人哭笑不得。但是有一说一还是唱的挺好听的，就是不知道为什么一看她闭眼就想笑。
![thank you for the great deepfake template](/img/deepfake/comment.png)
## 运行代码

打开这个链接：[Deepfake代码](https://colab.research.google.com/github/AliaksandrSiarohin/first-order-model/blob/master/demo.ipynb)
依次点击两个代码块左边的运行按钮，滚动到页面底部，等待一会儿后就会出来图像界面。
![依次点这两个播放按钮](/img/deepfake/run_script.png)
根据图形界面的指示，上传刚才准备好的图片和视频，点击`Generate`按钮就可以生成换脸视频了，需要等几分钟。如图所示，damedane的原版视频有预设了（奥巴马左边那位）。
![海猫填充完毕](/img/deepfake/gui2.png)
生成完成后，下载下来即可。
![海猫的痛，你们懂吗](/img/deepfake/compare.png)

## 参考
YouTube一搜Unravel就会有一个教程视频，然后找到了对应的文章。不过这个教程是快一年前的了，现在deepfake的脚本已经更新过了，不用这么麻烦了。
https://www.youtube.com/watch?v=MXNBtws35WE&t=518s
https://www.kapwing.com/resources/how-to-make-the-unravel-tokyo-ghoul-deepfake-meme/