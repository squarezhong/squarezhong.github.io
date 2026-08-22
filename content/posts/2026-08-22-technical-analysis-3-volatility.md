---
title: 技术分析（三）：波动率指标
date: 2026-08-22
tags:
  - Trade
author: Square Zhong
description: 风浪越大鱼越贵？
---

前两期分别介绍了趋势指标和动量指标，本期介绍两个常用的波动率指标：Bollinger Bands 和 ATR。

波动率用于描述价格变化的剧烈程度。

金融学语境中的波动率通常指收益率的标准差；技术分析对“波动”的使用更宽泛：Bollinger Bands 观察收盘价围绕移动平均线的离散程度，ATR 则观察单个周期内的真实波幅。

## 符号体系

沿用 [技术分析（一）：趋势指标](https://squarezhong.github.io/posts/2026-08-12-technical-analysis-1-trend/#符号体系) 中的符号体系。

## Bollinger Bands

Bollinger Bands，布林带/布林线，简称 BOLL，由 [John Bollinger](https://www.bollingerbands.com/) 在 20 世纪 80 年代提出。

BOLL 在一条移动平均线的上下各放置一条随波动率变化的轨道。波动增大时轨道变宽，波动减小时轨道收窄。默认参数通常为 $(N,K)=(20,2)$：$N$ 为周期数，$K$ 为轨道宽度。

### 计算方法

中轨为收盘价的 $N$ 周期[简单移动平均](https://squarezhong.github.io/posts/2026-08-12-technical-analysis-1-trend/#ma)：

$$
\text{MID}_N^{(t)}=\text{MA}_N^{(t)}
$$

最近 $N$ 个周期收盘价的标准差记为：

$$
\sigma_N^{(t)}=
\sqrt{
\frac{1}{N}
\sum_{i=0}^{N-1}
\left(\text{Close}^{(t-i)}-\text{MID}_N^{(t)}\right)^2
}
$$

上轨和下轨分别为：

$$
\text{UPPER}_N^{(t)}=\text{MID}_N^{(t)}+K\sigma_N^{(t)}
$$

$$
\text{LOWER}_N^{(t)}=\text{MID}_N^{(t)}-K\sigma_N^{(t)}
$$

$N$ 决定观察窗口的长度，$K$ 决定上下轨与中轨的距离。减小 $N$ 会让三条线对近期价格更加敏感；增大 $K$ 会让轨道整体变宽。

不同软件可能允许更换中轨的均线类型、计算数据源以及标准差的实现方式。比较 BOLL 数值时，需要确认参数和公式设置是否一致。

聪明的你可能已经发现了，这浓眉大眼的布林带怎么这么像正态分布下的 95% 置信区间呢？

还真是，对于正态分布 $X \sim N(\mu, \sigma^2)$，$P(\mu - 2\sigma < X < \mu + 2\sigma) \approx 95.45\%$。但是金融价格显然不符合稳定的正态分布，你应当将布林带理解为**价格偏离近期均线多少个标准差**，而不是所谓价格的 95% 置信区间。

### BandWidth

直接观察上下轨的距离会受到标的价格高低影响，可以用中轨对带宽进行归一化：

$$
\text{BandWidth}_N^{(t)}=
\frac{\text{UPPER}_N^{(t)}-\text{LOWER}_N^{(t)}}
{\text{MID}_N^{(t)}}\times100=
\frac{2K\sigma_N^{(t)}}{\text{MID}_N^{(t)}}\times100
$$

BandWidth 越大，代表近期收盘价相对中轨越分散；BandWidth 越小，代表价格越集中。它适合观察同一标的在不同时期的波动变化。

### %b

%b 用于量化当前收盘价在布林带中的相对位置：

$$
\%b_N^{(t)}=
\frac{\text{Close}^{(t)}-\text{LOWER}_N^{(t)}}
{\text{UPPER}_N^{(t)}-\text{LOWER}_N^{(t)}}
$$

- $\%b=1$：收盘价位于上轨
- $\%b=0.5$：收盘价位于中轨
- $\%b=0$：收盘价位于下轨
- $\%b>1$ 或 $\%b<0$：收盘价位于轨道之外

### 指标解读

- **收口（Squeeze）**：上下轨变窄，代表近期波动率下降、价格进入压缩状态。收口经常受到关注，因为低波动之后可能出现波动扩张，但它不提供突破方向，也无法确定突破时间。
- **开口**：上下轨距离扩大，代表波动率上升。
- **沿轨运行（Walking the Bands）**：强趋势中，价格可能连续贴近上轨或下轨运行。触碰上下轨并不意味着直接反转。
- **中轨**与均线：中轨在默认参数下就是 $\text{MA}20$。部分行情中我们会观察到价格下跌触及中轨后反弹，这个其实就相当于回踩 MA20（20 日均线）后反弹。触及下轨后的上涨也可以被视为偏离 MA20 较远后修复。

## ATR

Average True Range，平均真实波幅，由 [J. Welles Wilder Jr.](https://en.wikipedia.org/wiki/J._Welles_Wilder_Jr.) 提出，常用周期为 14。

我们在 [技术分析（一）：趋势指标](https://squarezhong.github.io/posts/2026-08-12-technical-analysis-1-trend/#tr) 中已经介绍了真实波幅 TR（True Range）：

$$
\text{TR}^{(t)} = \max \left(
\text{High}^{(t)}-\text{Low}^{(t)},
\left|\text{High}^{(t)}-\text{Close}^{(t-1)}\right|,
\left|\text{Low}^{(t)}-\text{Close}^{(t-1)}\right|
\right)
$$

ATR 对 TR 进行平滑，表示平滑移动平均后的真实波动幅度。

### 计算方法

ATR 计算使用平滑移动平均 SMMA（系数 $\alpha=1/N$）。第一个有效 ATR 通常采用最初 $N$ 个 TR 的简单平均。后续 ATR 计算如下：

$$
\text{ATR}_N^{(t)}=
\text{SMMA}_N^{(t)}(\text{TR})=
\frac{1}{N}\text{TR}^{(t)}+
\left(1-\frac{1}{N}\right)\text{ATR}_N^{(t-1)}
$$

### ATRP/NATR

ATR 的单位与价格相同。股价为 10 元的标的和股价为 1000 元的标的，即使相对波动完全一致，ATR 数值也会相差约 100 倍，不能直接横向比较。

将 ATR 除以当前收盘价，可以得到相对波动率：

$$
\text{ATRP}_N^{(t)}=
\frac{\text{ATR}_N^{(t)}}{\text{Close}^{(t)}}\times100
$$

这个指标常被称为 ATR Percent 或 Normalized ATR（NATR）。例如 ATRP = 3%，表示近期平均真实波幅约为当前价格的 3%。归一化之后可以更合理地比较同一标的不同时期，或比较价格尺度不同的标的。

### 指标解读

- ATR 上升代表近期单周期波动幅度扩大，ATR 下降代表波幅收窄。
- ATR 是近期波动的平均值，不代表下一个周期的最大涨跌幅。突发事件、跳空和流动性枯竭都可能让实际损失远超历史 ATR。

### 实际用途

#### 止损距离

固定百分比止损没有考虑标的自身的波动特征。若使用 ATR，可以将止损距离设置为 $K$ 倍 ATR：

$$
\text{Stop Distance}=K\times\text{ATR}
$$

波动较大时止损距离自动变宽，波动较小时自动收窄。$K$ 的取值需要结合交易周期、标的特征和策略回测确定。

#### 仓位控制

假设单笔交易最多承受金额为 $R$ 的损失，止损距离为 $K\times\text{ATR}$，不考虑手续费、滑点和合约乘数（期货期权）时，仓位数量可以写成：

$$
\text{Position Size}=
\frac{R}{\text{Stop Distance}}=
\frac{R}{K\times\text{ATR}}
$$

ATR 越高，单位仓位承担的波动越大，计算出的仓位越小。
