# NSHipster翻译说明

## 翻译流程

[NShipster](https://nshipster.com/)之前一直由[NSHipster.cn](https://nshipster.cn/)团队在负责翻译，我们现在是跟他们合作一起翻译。雨谨作为SwiftGG的代表，负责协调双方的交流。本着简便起见的原则，我们先把翻译做起来，以后再慢点完善流程。目前暂定的合作流程如下：

1. 收集待选文章。雨谨会挑选部分[NShipster](https://nshipster.com/)上的文章，大家发现了好的文章，也可以发给他。
2. 确定待选文章。雨谨把待选文章发给[NSHipster.cn](https://nshipster.cn/)，如果他们觉得没问题，我们就挂到GitHub的issues上，供大家认领。
3. 认领Issue。当我们有人要翻译某篇文章时，请先联系雨谨，由他跟[NSHipster.cn](https://nshipster.cn/)再次确认，避免重复工作。如果他们也没做，我们就可以开始按正常流程开始翻译了。
4. 提交给[NSHipster.cn](https://nshipster.cn/)。翻译完成后，按照他们的格式规范把译文处理一下，然后由雨谨提PR到他们的[GitHub](https://github.com/NSHipster/articles-zh-Hans)。
5. 同步发布到SwiftGG。

## [NSHipster.cn](https://nshipster.cn/)的译文规范

[NSHipster.cn](https://nshipster.cn/)对MarkDown格式的要求如下：

1. 使用原文的格式，并将**中文在英文下面**，方便校验
2. 英文、数字与中文之间（中文标点除外）加一个空格
3. 文章头部加上下面的内容

```
---
layout: post
title: NSDateComponents
category: Cocoa
author: Mattt
translator: Candyan
---
```

更多细节可以参考他们的[GitHub](https://github.com/NSHipster/articles-zh-Hans)。