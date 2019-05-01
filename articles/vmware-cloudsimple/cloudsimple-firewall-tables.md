---
title: 防火墙表-通过 CloudSimple VMware 解决方案-Azure
description: 了解有关 CloudSimple 私有云防火墙表和防火墙规则。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577340"
---
# <a name="firewall-tables-overview"></a>防火墙表概述

防火墙表列出了用于筛选传入和传出私有云资源的网络流量规则。 可以将其应用到一个 VLAN 或子网。 然后，这些规则控制源网络或 IP 地址和目标网络或 IP 地址之间的网络流量。

## <a name="firewall-rules"></a>防火墙规则

下表介绍了防火墙规则中的参数。

| 属性 | 详细信息 |
| ---------| --------|
| **名称** | 唯一标识该防火墙规则，并且它的用途的名称。 |
| **Priority** | 介于 100 和 4096，与最高优先级的 100 之间的数字。 规则按优先顺序处理。 当流量会通过规则匹配时，将停止处理规则。 因此，不被处理优先级较低的优先级较高的规则相同的属性存在任何规则。  请注意避免冲突规则。 |
| **状态跟踪** | 跟踪可以是无状态 （私有云、 Internet 或 VPN） 或有状态 (公共 IP)。  |
| 协议 | 选项包括任何，TCP 或 UDP。 如果需要 ICMP，则使用任何。 |
| **Direction** | 该规则是应用到入站还是出站流量。 |
| **Action** | 允许或拒绝流量规则中定义的类型。 |
| **源** | IP 地址、 无类域间路由 (CIDR) 块 (10.0.0.0/24，例如)，或 Any。  指定的范围，服务标记或应用程序安全组，可创建较少的安全规则。 |
| **源端口** | 从哪些网络流量来源的端口。  您可以指定单个端口或范围的端口，例如 443 或 8000 8080。 指定范围可以减少创建的安全规则数。 |
| **目标** | IP 地址、 无类域间路由 (CIDR) 块 (10.0.0.0/24，例如)，或 Any。  指定的范围，服务标记或应用程序安全组，可创建较少的安全规则。  |
| **目标端口** | 端口的网络流量流。  您可以指定单个端口或范围的端口，例如 443 或 8000 8080。 指定范围可以减少创建的安全规则数。|

### <a name="stateless"></a>无状态

无状态的规则只能在单个数据包看起来并筛选它们根据该规则。  
其他规则可能需要按反方向的流量流。  下面的点之间的流量使用无状态的规则：

* 子网的私有云
* 在本地子网和私有云子网
* 从私有云的 Internet 流量

### <a name="stateful"></a>有状态

 有状态规则会了解通过它传递的连接。 将为现有连接创建流记录。 是允许还是拒绝通信取决于流记录的连接状态。  使用此规则类型来筛选流量从 Internet 的公共 IP 地址。

### <a name="default-rules"></a>默认规则

在防火墙中的每个表上创建以下默认规则。

|优先度|名称|状态跟踪|Direction|流量类型|Protocol|源|Source Port|目标|Destination Port|操作|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|allow-all-to-internet|有状态|出站|公共 IP 或 internet 流量|全部|任意|任意|任意|任意|允许|
|65001|deny-all-from-internet|有状态|入站|公共 IP 或 internet 流量|全部|任意|任意|任意|任意|拒绝|
|65002|allow-all-to-intranet|无状态|出站|内部私有云或 VPN 流量|全部|任意|任意|任意|任意|允许|
|65003|allow-all-from-intranet|无状态|入站|内部私有云或 VPN 流量|全部|任意|任意|任意|任意|允许|

## <a name="next-steps"></a>后续步骤

* [设置防火墙表和规则](https://docs.azure.cloudsimple.com/firewall/)