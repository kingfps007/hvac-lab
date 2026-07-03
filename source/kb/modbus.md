---
title: MODBUS通信协议
type: kb
layout: page
order: 5
---

MODBUS是楼宇自动化领域最广泛使用的串行通信协议之一。本知识库基于MODBUS官方规范 (Modbus Application Protocol Specification V1.1b3)，涵盖RTU/TCP两种模式的帧格式、功能码及暖通设备通信实例。

## 1. MODBUS协议概述

<blockquote style="background:var(--primary-bg);border-left:4px solid var(--primary);padding:10px 16px;margin:12px 0;">
MODBUS由Modicon（现施耐德电气）于1979年发布，是工业自动化领域最流行的应用层协议。采用主-从(Master-Slave)或客户端-服务器(Client-Server)架构，免费开放使用。
</blockquote>

### 两种传输模式对比

| 特性 | MODBUS RTU | MODBUS TCP |
|------|-----------|-----------|
| **物理层** | RS-485 / RS-232 | Ethernet (IEEE 802.3) |
| **地址空间** | 1-247 (从站地址) | IP地址 + 端口502 |
| **校验方式** | CRC-16 (循环冗余校验) | TCP/IP层校验 (MBAP头无CRC) |
| **帧间隔** | ≥3.5字符时间(静默间隔) | 无(依赖TCP流) |
| **最大速率** | 115.2 kbps (典型9600/19200) | 100 Mbps / 1 Gbps |
| **暖通典型应用** | DDC↔电表/热量表/VFD/传感器 | DDC↔上位机BAS/BMS/云平台 |
| **一主多从** | 1个主站+最多247从站(单RS-485总线32节点/段) | 多客户端并发 |

## 2. MODBUS RTU 帧格式

### RTU报文结构（标准帧）

| 字段 | 长度 | 说明 | 示例(读保持寄存器) |
|------|------|------|------------------|
| 地址域 | 1 byte | 从站地址 1-247 (0=广播) | 0x01 |
| 功能码 | 1 byte | 1-255 (标准1-127) | 0x03 |
| 数据域 | N bytes | 取决于功能码 | 0x00 0x00 0x00 0x02 |
| CRC校验 | 2 bytes | CRC-16-Modbus (低位在前) | 0xC4 0x0B |

**完整帧示例**(读取1#从站保持寄存器40001-40002):
`01 03 00 00 00 02 C4 0B`

### CRC-16-Modbus 校验算法

CRC-16采用多项式 0xA001 (反转CRC-16-IBM 0x8005)。

```javascript
// CRC-16-Modbus 计算（JavaScript参考实现，用于DDC/BMS系统编程）
function crc16Modbus(buf) {
    let crc = 0xFFFF;        // 初始值全1
    for (let i = 0; i < buf.length; i++) {
        crc ^= buf[i];
        for (let j = 0; j < 8; j++) {
            if (crc & 0x0001)  crc = (crc >> 1) ^ 0xA001;
            else               crc >>= 1;
        }
    }
    return crc;  // 低字节在前
}
```

## 3. MODBUS TCP 报文结构

### MBAP头 + PDU

| 字段 | 长度 | 说明 |
|------|------|------|
| 事务标识符 | 2 bytes | 请求-应答配对序号(客户端生成) |
| 协议标识符 | 2 bytes | 固定0x00 0x00 (MODBUS) |
| 长度 | 2 bytes | 后续字节数(单元ID+功能码+数据) |
| 单元标识符 | 1 byte | 相当于RTU的从站地址(串口网关用) |
| 功能码+数据 | N bytes | 与RTU相同的PDU |

**TCP请求示例**(读保持寄存器):
`00 01 00 00 00 06 01 03 00 00 00 02`
MBAP头: 事务=1, 协议=0, 长度=6, 单元=1 | PDU: 功能码=03, 起始地址=0, 数量=2

## 4. MODBUS功能码速查

| 功能码 | 名称 | 寄存器类型 | 操作 | 暖通典型用途 |
|-------|------|-----------|------|------------|
| **01 (0x01)** | 读线圈 | Coil(0x) | 读1～2000个 | 读设备启停状态、开关阀状态 |
| **02 (0x02)** | 读离散输入 | Discrete Input(1x) | 读1～2000个 | 读报警状态、限位开关、水流开关 |
| **03 (0x03)** | 读保持寄存器 | Holding Register(4x) | 读1～125个 | 读温度/湿度/压力/流量/CO₂值、VFD频率 |
| **04 (0x04)** | 读输入寄存器 | Input Register(3x) | 读1～125个 | 读传感器原始AD值、电能表累计值 |
| **05 (0x05)** | 写单线圈 | Coil(0x) | 写1个(ON/OFF) | 远程启停设备 |
| **06 (0x06)** | 写单寄存器 | Holding Register(4x) | 写1个(16-bit) | 设定温度值、PID参数、VFD频率 |
| **15 (0x0F)** | 写多线圈 | Coil(0x) | 写多个 | 批量控制多个设备启停 |
| **16 (0x10)** | 写多寄存器 | Holding Register(4x) | 写多个 | 批量写入运行参数、时间表 |

<blockquote style="background:#FFF3E0;border-left:4px solid var(--accent);padding:10px 16px;margin:12px 0;">
MODBUS寄存器地址通常以1为基(如40001)，在线路上以0为基传输(40001→地址0x0000)。功能码03读4xxxx、04读3xxxx。
</blockquote>

## 5. 暖通常用数据格式

| 数据类型 | 说明 |
|---------|------|
| **16-bit整数** | 1个寄存器，范围-32768～32767。例: 温度×10存储(250→25.0°C) |
| **32-bit浮点** | 2个连续寄存器(IEEE 754 float)，字节序需确认(大端/小端/混合) |
| **32-bit整数** | 2个连续寄存器，用于电能累计(kWh)、流量累计(m³) |
| **字节序** | Big-Endian、Little-Endian、Big-Endian Byte Swap、Little-Endian Byte Swap — 不同厂家不同，需查手册 |

## 6. 暖通设备MODBUS通信实例

### 例1: 读取变频器(VFD)运行频率

从站地址=1，读取保持寄存器地址0x1001(运行频率，单位0.01Hz)

- **请求**: `01 03 10 01 00 01 90 CB`
- **响应**: `01 03 02 13 88 B5 B2` → 数据0x1388=5000 → 频率**50.00Hz**

### 例2: 读取温湿度传感器

从站地址=2，读取保持寄存器0x0000(温度)和0x0001(湿度)

- **请求**: `02 03 00 00 00 02 C4 38`
- **响应**: `02 03 04 01 0A 02 35 78 9A` → 温度=0x010A/10=**26.6°C**, 湿度=0x0235/10=**56.5%RH**

### 例3: 远程启动AHU风机

从站地址=1，写单线圈地址0x0001=ON

- **请求**: `01 05 00 01 FF 00 DD FA`
- **响应**(回显确认): `01 05 00 01 FF 00 DD FA`

## 参考文献

1. Modbus Organization, *MODBUS Application Protocol Specification V1.1b3*, April 2012. [modbus.org](https://modbus.org/docs/Modbus_Application_Protocol_V1_1b3.pdf)
2. Modbus Organization, *MODBUS over Serial Line—Specification and Implementation Guide V1.02*, December 2006.
3. GB/Z 19582-2008, 《基于Modbus协议的工业自动化网络规范》(共3部分), 中国国家标准化管理委员会, 2008.
4. Schneider Electric, *"Modbus Protocol Reference Guide"*, PI-MBUS-300 Rev. J, 1996.
5. ASHRAE, *ANSI/ASHRAE Standard 135-2020: BACnet*, Appendix H (BACnet/MODBUS Gateway), 2020.
6. Siemens, *"MODBUS RTU Communication for Sentron PAC Power Meters"*, Technical Manual, 2021.
