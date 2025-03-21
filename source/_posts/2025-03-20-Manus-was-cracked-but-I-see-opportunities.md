---
layout: post
title:  "Manus 被破解，但我看到的是机会！"
date:   2025-03-20 16:29:33
cover: https://img.jikehou.cn/cover/2025-03-20.png
categories: [AIGC研究]
tags: [AIGC]
keywords: [Manus, AIGC]
---

嗨，大家好，我是极客猴~

半个月之前，Manus 一夜爆火，引起全球科技圈震动。

现在热度慢慢降下来，我说点不一样的。

## 1，Manus 究竟是什么？

根据 Manus 团队的介绍，Manus 是一款通用型 AI 智能体产品。

那 AI 智能体又是什么？

我们在浏览器页面，跟 ChatGPT，豆包等人工智能进行纯文字对话，这些叫做语言大模型。

如果说大语言模型是“大脑”，AI智能体则更像一个“完整的人”，具备大脑（决策）+ 感官（感知）+ 手脚（执行）的综合能力。

AI 智能体，个人认为更接近于你的私人管家的角色。

有一个 Manus 的执行案例，输入相关的内容，Manus 就开始执行，并非常详细的计划。

```
I need a 7-day Japan itinerary for April 15-23 from Seattle, with a $2500-5000 budget for my fiancée and me. 

We love historical sites, hidden gems, and Japanese culture (kendo, tea ceremonies, Zen meditation). We want to see Nara's deer and explore cities on foot. 

I plan to propose during this trip and need a special location recommendation. 

Please provide a detailed itinerary and a simple HTML travel handbook with maps, attraction descriptions, essential Japanese phrases, and travel tips we can reference throughout our journey.
```

{% video https://img.jikehou.cn/img/20250320_1.mp4 %}


提问者的输入的大概内容是：

**我需要一份 4 月 15-23 日从西雅图出发的 7 天双人的日本行程，预算为 2500-5000 美元。**

**我们喜欢名胜古迹、隐藏的宝藏和日本文化（剑道、茶道、禅宗冥想）**

**最终提供一份详细的行程表和一本简单的 HTML 旅行手册，其中包含地图、景点介绍、必要的日语短语和旅行小贴士**

接着，你会看到 Manus 开始推理思考，创建 todo.md 文件，进行网络搜索，将内容进行整合，最后输出一份完整的 7 天旅行计划，呈现形式是网页。

我第一次看的时候，给我的感觉是，非常震撼！

特别是最后的网页呈现的内容，巨详细的：
- 有最近 7 天的当地天气预报。
- 便捷指南，包括紧急呼叫电话，汇率，当地时区等
- 有 7 天的早下晚的日常安排。
- 行程（飞机）的具体安排。
- 旅行地址的详细介绍。

当看完之后，我感觉这 Manus 的推理过程有点 DeepSeek 的深度思考的味道。

心想，DeepSeek 发布才过去多久，这么快就有基于 DeepSeek 的应用开发出来？

当有大神将 Manus 破解之后，发现 Manus 背后不是一个独立的大模型，是基于  Claude Sonnet 3.7 的基础上构建的。

**Manus 本质上还是"套壳"产品，基于大模型进行二次创新。**


## 2， 普通人的机会

Manus 刚出来的时候，还在内测，需要邀请码才能使用。

短短几天，一个内测邀请码就在二手平台被炒至 5 万元。

商业嗅觉灵敏的人，已经靠卖邀请码赚了一波。

慢人一步，就不能无法利用 AI 赚点小钱？

其实，DeepSeek 的 R1 大模型的推理思考能力，根本不弱于 Claude。

普通人能借助 DeepSeek 的 API 做一些“套壳”产品来赚点小钱，还是有巨大的机会。

能满足特定用户的需求，有需求就有市场。

市场也不用很大，太大容易被巨头盯上。

越垂直，越能简单解决痛点需求，越刚需。



