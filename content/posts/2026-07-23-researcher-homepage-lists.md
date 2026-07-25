---
title: 研究者个人主页方案调研
date: 2026-07-23
tags: ["Academic", "Homepage"]
author: Square Zhong
description: 适合自己的就是最好的
---

## 简介

一个非常不严谨的研究者个人主页方案分类。

## 汇总

缺点都是我乱写的，适合自己风格的就是最好的。

| 风格       | 代表人物                                                                                                     | 特点                                               | 方案                                                                                                                                                                                                                       | 优点                                 | 缺点                                              |
| -------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------- | ----------------------------------------------- |
| 学术档案式    | - [Yann LeCun](http://yann.lecun.com) <br>- [Geoffrey E. Hinton](https://www.cs.toronto.edu/~hinton/)    | - 这辈子的东西都在上面了<br>- 相当于给自己学术生涯做了个档案               | HTML + CSS + 少量 js                                                                                                                                                                                                       | - 页面加载速度快<br>- 搜索引擎易检索<br>- 信息极其丰富 | - 信息很多但访问者很难 get 到<br>- 看起来比较过时<br>- 移动端适配差     |
| 极简单页     | [何恺明](https://people.csail.mit.edu/kaiming/)                                                             | - 白色背景，大量文本和链接，基本没有图片<br>- 没有单纯列履历，展现的都是自己精选后的成果 | 同上<br>                                                                                                                                                                                                                   | - 风格极度克制（逼格比较高）<br>- 信息密度高<br>- 稳定 | - 对更复杂的渲染支持较差                                   |
| 不那么极简的单页 | - [Shuran Song](https://shurans.github.io/)<br>- [Andrej Karpathy](https://karpathy.ai/index.html)       | - 相比上面的视觉更丰富一些                                   | 同上                                                                                                                                                                                                                       | - 视觉信息更丰富<br>- 稳定                  | 同上                                              |
| 简洁多页     | - [唐杰](https://keg.cs.tsinghua.edu.cn/jietang/)<br>- [Fadel Adib](https://www.mit.edu/~fadel/index.html) | - 简洁风格<br>- 简介、论文、奖项、学生等分页展示                     | 同上                                                                                                                                                                                                                       | - 信息丰富且易获取                         | - 手动维护 多页 html 比较累（当然现在可以让 AI 帮忙）<br>- 做不了复杂的渲染 |
| 现代学术风    | 非常多 cs 领域年轻学者及 PhD<br>- [Zongyi Li](https://zongyi-li.github.io/)                                        | - 非常流行的学术主页模板<br>                                | Hugo ([HUgo Blox](https://hugoblox.com/templates/academic-cv)) or Jekyll ([academicpages](https://github.com/academicpages/academicpages.github.io) / [al-folio](https://alshedivat.github.io/al-folio/)) + Github Pages | - 全平台适配<br>- 全面、清晰展示简介、论文、学生情况     | - 模板用的人太多了，略微同质化                                |
| 视觉驱动风格   | - [Yoshua Bengio](https://yoshuabengio.org/en)<br>- [Karen Liu](https://tml.stanford.edu/)               | - 视觉效果好                                          | - Wordpress<br>- 自定义前后端方案                                                                                                                                                                                                | - 视觉冲击力强<br>- 交互优秀                 | - 加载速度慢<br>                                     |

## 部署方式

建议不要租用服务器（来自个人博客多次烂尾的教训），直接部署在学校服务器或者 Github Pages 上。

## 个人主页和实验室主页

除非是很大的组，不然实验室人员放在个人主页一起讲比较好，分成两个网站维护成本太高了。
