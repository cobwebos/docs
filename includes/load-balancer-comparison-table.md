---
title: include 文件
description: include 文件
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734502"
---
| | 标准 SKU | 基本 SKU |
| --- | --- | --- |
| 后端池大小 | 最多支持 1000 个实例。 | 最多支持 100 个实例。 |
| 后端池终结点 | 单个虚拟网络中的任何虚拟机，包括虚拟机、可用性集和虚拟机规模集的混合。 | 单个可用性集或虚拟机规模集中的虚拟机。 |
| [运行状况探测](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [运行状况探测停止行为](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP 连接在实例探测停止时以及在所有探测停止时保持活动状态。 | TCP 连接在实例探测停止时保持活动状态。 所有 TCP 连接在所有探测停止时都会终止。 |
| 可用性区域 | 在入站和出站的标准 SKU、区域冗余和区域前端中，出站流映射在发生区域故障后仍保留，并且跨区域进行负载均衡。 | 不可用。 |
| 诊断 | Azure Monitor、多维度指标（包括字节和数据包计数器）、运行状况探测状态、连接尝试 (TCP SYN)、出站连接运行状况（SNAT 成功和失败流）、活动数据平面度量 | 仅用于公共负载均衡器的 Azure Log Analytics、SNAT 耗尽警报、后端池运行状况计数。 |
| HA 端口 | Internal 负载均衡器（内部负载均衡器） | 不可用。 |
| 默认保护 | 公共 IP、公共负载均衡器终结点、内部负载均衡器终结点会阻止入站流，除非入站流已由某个网络安全组列入允许列表。 | 默认打开，网络安全组可选。 |
| [出站连接](../articles/load-balancer/load-balancer-outbound-connections.md) | 可以使用[出站规则](../articles/load-balancer/load-balancer-outbound-rules-overview.md)显式定义基于池的出站 NAT。 可以在每个负载均衡规则选择退出时使用多个前端。_必须_显式创建出站方案，虚拟机、可用性集、虚拟机规模集才能使用出站连接。  虚拟网络服务终结点无需定义出站连接便可访问，且不会计入已处理的数据。  任何公共 IP 地址（包括不作为 VNet 服务终结点提供的 Azure PaaS 服务）必须通过出站连接才能访问，且计入处理的数据。 如果只有一个内部负载均衡器为虚拟机、可用性集或虚拟机规模集提供服务，则经由默认 SNAT 的出站连接将不可用，请改用[出站规则](../articles/load-balancer/load-balancer-outbound-rules-overview.md)。 出站 SNAT 编程特定于传输协议，并以入站负载均衡规则的协议为基础。 | 单个前端，存在多个前端时随机选择。  如果只有内部负载均衡器为虚拟机、可用性集或虚拟机规模集提供服务，则会使用默认 SNAT。 |
| [出站规则](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 使用公共 IP 地址或公共 IP 前缀或以上两者、可配置出站空闲超时（4-120 分钟）或自定义 SNAT 端口分配的声明性出站 NAT 配置 | 不可用。 |
|  [在空闲时重置 TCP](../articles/load-balancer/load-balancer-tcp-reset.md) | 对任何规则启用空闲超时时重置 TCP (TCP RST) | 不可用 |
| [多个前端](../articles/load-balancer/load-balancer-multivip-overview.md) | 入站和[出站](../articles/load-balancer/load-balancer-outbound-connections.md) | 仅限入站 |
| 管理操作 | 大多数操作都小于 30 秒 | 通常为 60 - 90 多秒。 |
| SLA | 对拥有两个正常运行的虚拟机的数据路径为 99.99%。 | 不适用。 | 
| 定价 | 基于规则数、与资源关联且经过入站和出站处理的数据量进行计费。  | 免费。 |
|  |  |  |
