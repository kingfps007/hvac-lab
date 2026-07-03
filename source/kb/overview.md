---
title: 建筑自控总览
type: kb
layout: page
order: 0
permalink: /kb/
---

建筑自动化系统（BAS）是智能建筑的核心神经系统。本知识库覆盖从传感器、执行器到控制算法、通信协议的完整技术链路，按以下三个层次组织，所有内容基于 ASHRAE 标准及公开学术文献。

## 感知与执行层

| 模块 | 核心内容 |
|------|----------|
| [🌡️ 暖通传感器](./sensors.html) | 温度(RTD/NTC/热电偶)、湿度(电容式)、压力/压差、流量、CO₂(NDIR)、空气质量 |
| [⚙️ 暖通常用执行器](./actuators.html) | 电动调节阀(Kvs选型/阀权度/流量特性)、风阀执行器、VFD变频器 |

## 控制与算法层

| 模块 | 核心内容 |
|------|----------|
| [🔄 控制回路](./control-loop.html) | 反馈/前馈/串级控制回路、DDC控制器I/O配置、暖通典型回路速查 |
| [🧠 控制算法](./algorithms.html) | PID整定、模糊控制、MPC模型预测控制、自适应控制、DRL强化学习 |

## 通信协议层

| 模块 | 核心内容 |
|------|----------|
| [🔌 MODBUS通信协议](./modbus.html) | RTU/TCP帧格式、CRC-16校验、功能码详解、暖通设备通信实例 |

## 推荐学习路径

1. **传感器**（知道DDC"看到"什么）→ 2. **执行器**（知道DDC"驱动"什么）→ 3. **控制回路**（传感器+控制器+执行器=闭环）→ 4. **控制算法**（PID如何整定、MPC为何节能）→ 5. **MODBUS协议**（设备间如何"对话"）

## 核心参考文献

`ASHRAE Handbook 2020/2023` `ASHRAE Standard 135-2020 BACnet` `ASHRAE Standard 62.1-2022` `ASHRAE Guideline 13-2015` `MODBUS Spec V1.1b3` `GB/Z 19582-2008` `GB/T 50314-2015` `IEC 60751:2022` `Seborg et al. 2016` `Åström & Hägglund 1995` `Drgoňa et al. 2020` `Afram & Janabi-Sharifi 2014` `Belimo Hydronic Guide 2023` `ABB Technical Guide No.4` `Fraden 2016` `陆耀庆 2008`
