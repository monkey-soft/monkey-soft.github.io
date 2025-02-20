---
layout: post
title:  "GitHub 标星10k+，新型冠状病毒（2019-nCoV）最全的数据集在这里！"
date:   2020-03-17 16:01:24
urlname: 115
cover: https://img.jikehou.cn/cover/2020-03-17.png
categories: [数据分析]
tags: [数据分析, Python]
keywords: [数据分析, 可视化, 数据集, Python, Github]
---
2020 年的春节是不平凡的。

新型冠状病毒（2019-nCoV）突然降临武汉，随着春运大潮，逐渐扩散到全国各省份。

这让原本应该是热热闹闹的春节，一下子气氛冷到冰点，感觉空气中都带着恐怖的气息。我们国家在疫情面前表现出强大一面，采取封城措施，举国禁足，共同对抗疫情。

经过一个月多的努力，目前疫情在国内算是得到控制。但海外情况却不容乐观，欧洲发达国家，英国、瑞典宣布放弃抵抗新冠病毒。

容许我大声喊一句：中国牛逼！我为自己身为中国人而感到骄傲！

我自己是学技术出身，想利用优势来获取全球疫情的第一手数据。

几经寻觅之后，我发现 GitHub 有个开源项目 **COVID-19**。这是美国约翰·霍普金斯大学系统科学与工程中心（JHU CSSE）收集各国卫生机构公开 2019 年新型冠状病毒的数据存储项目。

项目是出于教育和学术研究目的而开源，还提供了可视仪表板 Web 界面。不得不说是一个很良心的项目。
<!-- more -->
截止文章发布前半个小时，我们通过仪表盘数据可知，全球有 18 万人确诊，7155 人死亡，79433 人治愈。

![全球有 18 万人确诊](https://img.jikehou.cn/img/20200317_1.jpg)

意大利也是重灾区，伊朗、西班牙、韩国、德国、法国等也有超过 6 千人确证。

![意大利](https://img.jikehou.cn/img/20200317_2.jpg)

通过全球疫情总览图，我们可以知道新型冠状病毒已经席卷全球。

![全球疫情总览图](https://img.jikehou.cn/img/20200317_3.jpg)

但可喜的是，国内的疫情已经得到控制。

橙色的线条可以看出，2月14号开始确诊人数出现平缓趋势；2月28号以后就显示为平滑的曲线，说明疫情暂时得到控制。

![曲线](https://img.jikehou.cn/img/20200317_4.jpg)

关于这个项目，一共有三个数据集，分别是 `archived_data`、`csse_cvoid_19_data`、`who_covid_19_situation_reposts`。

![一共有三个数据集](https://img.jikehou.cn/img/20200317_5.jpg)

- archived_data 是2020年1月21日至2月14日有关冠状病毒COVID-19（以前称为2019-nCoV）的仪表板案例报告。其中 archived_time_series 目录是经过清洗之后的数据。
- csse_cvoid_19_data 是每日病例数据数据集，每隔24小时更新一次。其中 csse_covid_19_daily_reports 目录下有从1月22号至今每天的病例数据，而csse_covid_19_time_series 是经过清洗的数据集。如果我们想利用 Python 对疫情数据做可视化处理，可以直接选用该数据集。
- who_covid_19_situation_reports 则是 世卫组织 COVID-19 状况报告。

最后附上数据可视化仪表盘地址：

PC 端：[PC版可视化仪表板盘](https://www.arcgis.com/apps/opsdashboard/index.html#/bda7594740fd40299423467b48e9ecf6)

手机端：[手机版可视化仪表](http://www.arcgis.com/apps/opsdashboard/index.html#/85320e2ea5424dfaaa75ae62e5c06e61)


{% btn 'https://github.com/CSSEGISandData/COVID-19', COVID-19 项目仓库地址, far fa-hand-point-right,blue larger %}