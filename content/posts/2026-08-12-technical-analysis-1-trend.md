---
title: 技术分析（一）：趋势指标
date: 2026-08-12
tags:
  - Trade
author: Square Zhong
description: 于我而言主要是增强持仓信心
---
本期介绍一些常用的趋势指标。
需要注意的是趋势指标通常是滞后的，多用于**确认突破**而不是**预测突破**。

顺便吐槽一下国内主流的几个看盘软件，软件所用的技术指标连个文档都很难找。

## MA/EMA

### MA

(Simple) Moving Average，（简单）移动平均，简称 MA 或 SMA

$MA_N$ = 前 $N$ 个价格的未加权平均数

MA120 即过去 120 个交易日收盘价格的平均数。

均线是历史价格的平滑，最基础的趋势线，常用于判断支撑位、阻力位以及均线多空排列。
- 多头排列（看涨趋势）：短期均线在上，长期均线在下
- 空头排列（看跌趋势）：短期均线在下，长期均线在上

均线计算包括当前交易日，交易时间内通常用最新价参与均线计算，所以盘中均线末端会出现变动。

### EMA

Exponential Moving Average，指数移动平均，后面会用到。

相比于粗暴的 SMA，EMA 赋予了价格“近大远小”的加权。

$$
\text{EMA}_N^{(t)} = \text{Close}^{(t)} \times \alpha + \text{EMA}_N^{(t-1)} \times (1 - \alpha)
$$
其中：
- $\text{Close}$ 为当天的收敛价格/收盘价
- $t$ 为第 t 个交易日
- $N$ 为时间周期，比如 MACD 常用的 12，26，9
- $\alpha = \frac{2}{N + 1}$ 为平滑系数 

### 双均线策略

非常经典且简单的量化策略，选择一条长周期均线，一条短周期均线，短线上穿长线时买入，反之卖出。在 MACD 语境下也就是 DIFF > 0 买入，DIFF < 0 卖出。

实际操作时还会涉及基准价格类型、信号过滤、单次建仓比例等更细节的参数设定。

## MACD 

MACD，全称 Moving Average Convergence / Divergence，中文名大概是“指数平滑移动平均线”，由 [Gerald Appel](https://cmtassociation.org/presenter/gerald-appel/) 发明。

你在股票软件中看到的 MACD 后面还会带着三个数字，这是 MACD 的三个参数，通常为 $(12, 26, 9)$

MACD 图形上共有三个组成元素：

### DIFF（快线）

$$
\text{DIFF} = \text{EMA}_{12} - \text{EMA}_{26}
$$
有的看盘软件不会直接显示这条线，而是显示为柱状图，大于 0 则代表短周期 EMA 超过了长周期 EMA。

### DEA（慢线/信号线）

Difference Exponential Average

对 DIFF 再计算一次时间周期为 9 日的指数移动平均，过滤掉部分随机噪声

$$
\text{DEA} = \text{EMA}_9(\text{DIFF})
$$
### MACD

国内看盘软件的主流计算方式是将 DIFF 和 DEA 做差后乘以 2

$$
\text{MACD} = (\text{DIFF} - \text{DEA}) \times 2
$$
MACD 值通常以柱状图的形式呈现：
- 首次 MACD > 0 代表 DIFF 上穿 DEA 线（金叉），通常为看涨信号
- 首次 MACD < 0 代表 DIFF 下穿 DEA 线（死叉），通常为看跌信号

### 参数调整

按照 [Wiki](https://en.wikipedia.org/wiki/MACD) 所说，$(12,26,9)$ 的参数选择是历史遗留，因为历史上有一段时间一周有 6 个交易日，12 对应双周，26 对应一个月，9 对应 1.5 周。

这三个参数都是可以自由调整的，你可以针对你想要交易的标的进行回测，挑选出更合适的参数组合，但是要小心过拟合。

## DMI

Directional Movement Index，动向指标/趋向指标，由 [J. Welles Wilder Jr.](https://en.wikipedia.org/wiki/J._Welles_Wilder_Jr.) 提出。

DMI 通常包含 +DI（同花顺显示为 DI1）、-DI（同花顺显示为 DI2）、ADX 和 ADXR 这四个指标，其中：

- +DI 和 -DI 是多空指标
- ADX 和 ADXR 用于观察趋势的强度（而不是方向）

国内软件所使用的 DMI 指标通常包含两个参数，默认为 $(N,M)=(14,6)$：

- $N$ 为 TR、+DM、-DM 以及 +DI、-DI 的统计周期
- $M$ 为 DX 计算 ADX 时的平滑周期，同时也是 ADXR 的间隔周期

需要注意的是，不同软件的 DMI 实现并不完全一致。Wilder 原版使用类似 EMA 的 Wilder 平滑，国内软件常见公式则使用滚动求和与简单移动平均。即使参数相同，最终数值也可能略有差异。下面按照国内软件常见的 $(14,6)$ 公式进行介绍。

### 前置指标

#### TR

True Range，真实波幅

$$
\text{TR}^{(t)} = \max \left(
\text{High}^{(t)}-\text{Low}^{(t)},
\left|\text{High}^{(t)}-\text{Close}^{(t-1)}\right|,
\left|\text{Low}^{(t)}-\text{Close}^{(t-1)}\right|
\right)
$$

TR 取以下三个数值中的最大值：当天振幅、当天最高价与前一日收盘价的距离、当天最低价与前一日收盘价的距离。因此隔夜跳空也会被计入波动。

#### +DM & -DM

Directional Movement，方向变动。先分别计算最高价向上突破和最低价向下突破的距离：

$$
\text{UpMove}^{(t)}=\text{High}^{(t)}-\text{High}^{(t-1)}
$$

$$
\text{DownMove}^{(t)}=\text{Low}^{(t-1)}-\text{Low}^{(t)}
$$

如果 UpMove 和 DownMove 同时为正，只保留更大的方向，避免同一个周期被同时计入多空两边：

$$
+\text{DM}^{(t)}=
\begin{cases}
\text{UpMove}^{(t)}, & \text{UpMove}^{(t)}>0 \text{ 且 } \text{UpMove}^{(t)}>\text{DownMove}^{(t)} \\
0, & \text{其他情况}
\end{cases}
$$

$$
-\text{DM}^{(t)}=
\begin{cases}
\text{DownMove}^{(t)}, & \text{DownMove}^{(t)}>0 \text{ 且 } \text{DownMove}^{(t)}>\text{UpMove}^{(t)} \\
0, & \text{其他情况}
\end{cases}
$$

即只存在一个方向为正，另一个方向为 0

### +DI/DI1/PDI

Positive Directional Indicator

$$
+\text{DI}_N^{(t)}=
\frac{\sum_{i=0}^{N-1}+\text{DM}^{(t-i)}}{\sum_{i=0}^{N-1}\text{TR}^{(t-i)}}\times100
$$

+DI 衡量最近 $N$ 个周期中，上升方向变动占真实波幅的比例。数值越大，代表向上突破的力量越强。

### -DI/DI2/NDI

Negative Directional Indicator

$$
-\text{DI}_N^{(t)}=
\frac{\sum_{i=0}^{N-1}-\text{DM}^{(t-i)}}{\sum_{i=0}^{N-1}\text{TR}^{(t-i)}}\times100
$$

-DI 衡量最近 $N$ 个周期中，下降方向变动占真实波幅的比例。数值越大，代表向下突破的力量越强。

### ADX

Average Directional Index，平均趋向指标。

先计算 +DI 与 -DI 的相对差距：

$$
\text{DX}^{(t)}=
\frac{\left|+\text{DI}^{(t)}-(-\text{DI}^{(t)})\right|}
{+\text{DI}^{(t)}+(-\text{DI}^{(t)})}\times100
$$

再对 DX 计算 $M$ 周期移动平均：

$$
\text{ADX}_M^{(t)}=\text{MA}_M(\text{DX})
$$

经我人工验算，同花顺软件确实是使用周期为 $M=6$ 的简单移动平均来计算 ADX。

DX 在计算时取了绝对值，所以 ADX 只表示趋势是否明显，并不表示趋势方向。ADX 上升代表 +DI 与 -DI 的差距正在扩大，当前方向的趋势在增强；ADX 下降代表两者差距正在收窄，趋势在减弱或市场进入震荡。

### ADXR

Average Directional Index Rating，ADX 的再次平滑：

$$
\text{ADXR}^{(t)}=\frac{\text{ADX}^{(t)}+\text{ADX}^{(t-M)}}{2}
$$

ADXR 比 ADX 更平滑，也更加滞后：

- ADX > ADXR，代表当前趋势强度高于 $M$ 个周期之前
- ADX < ADXR，代表当前趋势强度低于 $M$ 个周期之前

### 指标解读

DMI 的使用顺序可以概括为：**先用 ADX 判断市场是否存在趋势，再用 +DI 与 -DI 判断趋势方向。**

- +DI > -DI：多方占优，反之空方占优
- +DI 上穿 -DI：空转多，反之多转空
- ADX < 20：通常认为趋势较弱，DI 交叉容易产生假信号
- ADX > 25：通常认为趋势已经比较明显，此时再结合 DI 的相对位置判断方向

- 20 和 25 只是经验阈值，不同标的、周期和波动结构下应当通过历史数据重新校准。
- ADX 高位回落只代表趋势强度下降，不等于价格马上反转。
- DMI 本质上仍然是价格的平滑与二次平滑，信号必然滞后。更适合用来过滤震荡行情、确认趋势，而不是单独预测顶部和底部。

## TD 序列

[TD Sequential](https://demark.com/sequential-indicator/)，即 TD 序列，由 [Tom DeMark](https://demark.com/) 提出，用于寻找一段趋势可能进入衰竭的区域。

完整的 TD Sequential 主要分为两个阶段：连续计数到 9 的 TD Setup，以及随后非连续计数到 13 的 TD Countdown。

### TD Setup

同花顺等中国看盘软件将其称为 **“神奇九转”**。

TD Setup 将当天收盘价与 4 个周期前的收盘价进行比较。

上涨计数的条件为：

$$
\text{Close}^{(t)}>\text{Close}^{(t-4)}
$$

如果这个条件连续满足 9 个周期，就依次标记为 1 至 9，构成 TD Sell Setup，代表上涨趋势可能开始衰竭。

下跌计数的条件则相反：

$$
\text{Close}^{(t)}<\text{Close}^{(t-4)}
$$

如果这个条件连续满足 9 个周期，就构成 TD Buy Setup，代表下跌趋势可能开始衰竭。

计数期间只要条件不再满足，当前 Setup 就会中断并重新等待下一次计数。

### Perfected Setup

原版 TD Sequential 还会判断 Setup 是否“完善”（Perfected）：

- Buy Setup：第 8 或第 9 根 K 线的最低价，低于第 6、7 根 K 线的最低价（其实应该用不高于，但是太不直观了，所以用了不太严谨的说法）
- Sell Setup：第 8 或第 9 根 K 线的最高价，高于第 6、7 根 K 线的最高价

满足这个条件，说明价格在 Setup 尾端确实再次创出了**极值**；如果不满足，趋势可能还需要继续试探高点或低点。

### TD Countdown

完整的 TD Sequential 在 Setup 完成后还会继续进行 Countdown：

- Buy Countdown：当天收盘价低于 2 个周期前的最低价
- Sell Countdown：当天收盘价高于 2 个周期前的最高价

$$
\text{Buy Countdown}:\quad \text{Close}^{(t)}\leq\text{Low}^{(t-2)}
$$

$$
\text{Sell Countdown}:\quad \text{Close}^{(t)}\geq\text{High}^{(t-2)}
$$

Setup 完成当天（周期）也可以纳入 Countdown 计数， Countdown 最大累计到 13，不要求连续计数。原版规则还包括取消、重启和风险线等细节，不同软件的简化实现可能并不一致。

### 指标解读

- 相比于确认趋势，TD 序列更多的被理解为提示趋势的反转。
- 强趋势中价格可以在出现 9 之后继续沿原方向运行，甚至连续出现多个 9，最典型的例子莫过于美股的宽基指数（s&p 500、nasdaq 100 等）。
- 震荡行情中的反转提示通常比单边行情更有效。
