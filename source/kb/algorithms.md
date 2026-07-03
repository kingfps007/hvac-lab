---
title: 控制算法
type: kb
layout: page
order: 4
---

控制算法是建筑自动化系统的"大脑"。本知识库从经典PID到现代MPC，系统梳理暖通空调领域的主要控制算法原理、特点与工程应用。

## 1. PID控制（比例-积分-微分）

PID是暖通自控中使用率超过90%的基础算法，通过P/I/D三个环节的组合形成控制输出。

### PID控制律与参数作用

| 项目 | 说明 |
|------|------|
| **控制律** | u(t) = Kp·e(t) + (Kp/Ti)·∫e(t)dt + Kp·Td·de(t)/dt [位置式PID] |
| **Kp比例** | 快速响应，减小稳态误差。过大→振荡 |
| **Ti积分** | 消除稳态误差。过小→超调振荡 |
| **Td微分** | 预测偏差变化，抑制超调。对噪声敏感（暖通中PI更常用） |

### 参数整定方法

| 方法 | 说明 |
|------|------|
| **Ziegler-Nichols法** | 阶跃响应法(开环): 测量过程参数τ、T、K → 查表得Kp/Ti/Td。临界比例度法(闭环): 纯P增大至等幅振荡→临界增益Ku、振荡周期Tu→查表 |
| **Cohen-Coon法** | 针对一阶+滞后(FOPDT)模型优化，比Z-N的1/4衰减比更平滑 |
| **暖通工程经验** | AHU温度PI: Kp≈1～3, Ti≈120～300s; 静压PI: Kp≈0.5～2, Ti≈10～30s; 通常不启用D(微分) |
| **自适应整定** | DDC内置Auto-tuning功能: 施加阶跃/振荡信号 → 辨识模型 → 自动计算PID参数 |

> 参考文献: *Åström, K.J. & Hägglund, T., "PID Controllers: Theory, Design, and Tuning", 2nd Ed., ISA, 1995*

## 2. 模糊控制（Fuzzy Logic Control）

模糊控制将专家经验转化为IF-THEN规则，无需精确数学模型，适合难以建模的系统。

### 模糊控制架构

| 项目 | 说明 |
|------|------|
| **模糊化** | 将精确测量值(e, Δe)映射到模糊集合: NB(负大)、NS(负小)、ZE(零)、PS(正小)、PB(正大) |
| **规则库** | IF e IS NS AND Δe IS ZE THEN Δu IS NS ... (通常25～49条规则) |
| **推理+解模糊** | Mamdani最小推理 → 重心法(COG)解模糊 → 精确控制输出 |
| **暖通应用** | VAV风量控制、AHU温湿度耦合控制、地铁站环控优化 |
| **优势与局限** | 适应非线性、多变量系统；但规则依赖专家经验，缺乏严格稳定性保证 |

> 参考文献: *Afram, A. & Janabi-Sharifi, F., "Theory and applications of HVAC control systems–A review", Building and Environment, 72, 343-355 (2014)*

## 3. 模型预测控制（MPC）

MPC利用系统模型预测未来输出，在每个控制周期求解有限时域优化问题，是暖通节能的前沿方向。

### MPC原理与暖通应用

| 项目 | 说明 |
|------|------|
| **核心三要素** | ①预测模型(白箱/灰箱/黑箱); ②滚动优化(每步求解QP/MILP); ③反馈校正(测量值修正预测偏差) |
| **典型目标函数** | min Σ(能耗成本 + 热舒适惩罚 + 约束违反惩罚) |
| **暖通优势** | 可处理多变量(MIMO)、约束、时滞; 利用天气预报/电价预测做前瞻优化 |
| **节能潜力** | 文献报道相比传统PID/RBC可节能15-40% [Afram 2014, Killian 2016] |
| **暖通案例** | 冰蓄冷系统优化调度、区域供冷站MPC、办公楼主动蓄热夜间通风 |
| **工程挑战** | 需要建筑热模型(灰箱RC模型常用); 计算需求较大; 传感器需完备 |

> 参考文献: *Drgoňa, J. et al., "All you need to know about model predictive control for buildings", Annual Reviews in Control, 50, 2020*

## 4. 自适应控制与智能算法

### 4.1 自适应控制（Adaptive Control）

| 项目 | 说明 |
|------|------|
| **原理** | 在线辨识被控对象参数变化 → 自动调整控制器参数 |
| **类型** | MRAC(模型参考自适应)、STR(自校正调节器) |
| **暖通场景** | 季节性工况变化(夏/冬)、过滤器积尘导致风量变化 |

### 4.2 神经网络控制（NN / DRL）

| 项目 | 说明 |
|------|------|
| **NN辨识** | 多层感知器(MLP)学习HVAC系统的非线性输入-输出映射 |
| **DRL控制** | 深度强化学习：Agent通过试错学习最优策略 |
| **研究现状** | 学术前沿热点，工程落地仍面临安全性、可解释性挑战 |

> 参考文献: *Wang, Z. & Hong, T., "Reinforcement learning for building controls", Applied Energy, 269, 2020*

## 5. 控制算法在暖通中的对比

| 算法 | 复杂度 | 需要模型 | 多变量 | 约束处理 | 暖通成熟度 | 典型节能率 |
|------|-------|---------|-------|---------|----------|----------|
| **ON/OFF** | ★☆☆ | 否 | 否 | 否 | ★★★★★ | 基准 |
| **PID** | ★★☆ | 否(调参需) | 否(SISO) | 弱 | ★★★★★ | +5～15% |
| **模糊控制** | ★★★ | 规则库 | 可 | 可 | ★★★☆ | +10～20% |
| **MPC** | ★★★★ | 是(模型核心) | 是 | 是(核心优势) | ★★☆ | +15～40% |
| **自适应PID** | ★★★ | 在线辨识 | 否 | 弱 | ★★☆ | +10～20% |
| **DRL强化学习** | ★★★★★ | 环境交互 | 是 | 可 | ★☆☆☆ | 研究阶段 |

<blockquote style="background:var(--primary-bg);border-left:4px solid var(--primary);padding:10px 16px;margin:12px 0;">
节能率数据综合自Afram & Janabi-Sharifi (2014)、Drgoňa et al. (2020)等综述文献，实际效果因建筑和系统而异。
</blockquote>

## 参考文献

1. Åström, K.J. & Hägglund, T., *PID Controllers: Theory, Design, and Tuning*, 2nd Edition, ISA, 1995.
2. Afram, A. & Janabi-Sharifi, F., "Theory and applications of HVAC control systems–A review of model predictive control (MPC)", *Building and Environment*, 72, 343-355, 2014.
3. Drgoňa, J. et al., "All you need to know about model predictive control for buildings", *Annual Reviews in Control*, 50, 190-232, 2020.
4. Underwood, C.P., *HVAC Control Systems: Modelling, Analysis and Design*, E & FN Spon, London, 1999.
5. Wang, Z. & Hong, T., "Reinforcement learning for building controls", *Applied Energy*, 269, 115036, 2020.
6. Killian, M. & Kozek, M., "Ten questions concerning model predictive control for energy efficient buildings", *Building and Environment*, 105, 403-412, 2016.
7. 李树江, 秦泗钊, "基于模糊控制的暖通空调系统温湿度控制", 《暖通空调》, 33(4), 2003.
