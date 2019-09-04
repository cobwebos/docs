---
title: include 文件
description: include 文件
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: a37280fddf34221a9d08501ef55c6b069cd877a4
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242601"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>网络限制-Azure 资源管理器以下限制仅适用于通过**Azure 资源管理器**每个订阅的每个区域管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我们最近将所有默认限制提高到了最大限制。 如果没有最大限制列，则资源没有可调整的限制。 如果过去已通过客户支持提高了这些上限，因此在以下表中看不到更新的限制，可[免费提交联机客户支持请求](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | 默认/最大限制 | 
| --- | --- |
| 虚拟网络 |1,000 |
| 每个虚拟网络的子网数 |3,000 |
| 每个虚拟网络的虚拟网络对等互连数 |500 |
| 每个虚拟网络的 DNS 服务器数 |20 |
| 每个虚拟网络的专用 IP 地址数 |65,536 |
| 每个网络接口的专用 IP 地址数 |256 |
| 每个虚拟机的专用 IP 地址数 |256 |
| 每个网络接口的公共 IP 地址 |256 |
| 每个虚拟机的公共 IP 地址 |256 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |500,000 |
| 网络接口卡数 |65,536 |
| 网络安全组 |5,000 |
| 每个 NSG 的 NSG 规则数 |1,000 |
| 为安全组中的源或目标指定的 IP 地址和范围数 |4,000 |
| 应用程序安全组 |3,000 |
| 每个 IP 配置和每个 NIC 的应用程序安全组数 |20 |
| 每个应用程序安全组的 IP 配置数 |4,000 |
| 可在网络安全组的所有安全规则中指定的应用程序安全组数 |100 |
| 用户定义路由表数 |200 |
| 每个路由表的用户定义的路由数 |400 |
| 每个 Azure VPN 网关的点到站点根证书数 |20 |
| 虚拟网络 TAP |100 |
| 每个虚拟网络 TAP 的网络接口 TAP 配置 |100 |

#### <a name="publicip-address"></a>公共 IP 地址限制
| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| 公共 IP 地址数 - 动态 | 基本版为 1,000。 |联系支持人员。 |
| 公共 IP 地址数 - 静态 | 基本版为 1,000。 |联系支持人员。 |
| 公共 IP 地址数 - 静态 | 标准为1000。|联系支持人员。 |
| 公共 IP 前缀长度 | /28 | 联系支持人员。 |

#### <a name="load-balancer"></a>负载均衡器限制
以下限制仅适用于每个订阅按区域通过 Azure 资源管理器管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| Resource | 默认/最大限制 |
| --- | --- |
| 负载平衡器 | 1,000 | 
| 每个资源的规则数，基本 | 250 |
| 每个资源的规则数，标准 | 1,500 | 
| 每个 IP 配置的规则数 | 299 |
| 每个 NIC 的规则数 | 300 |
| 前端 IP 配置，基本 | 200 |
| 前端 IP 配置，标准 | 600 |
| 后端池，基本 | 100，单个可用性集 |
| 后端池，标准 | 1000，单一虚拟网络 |
| 每个负载均衡器的后端资源，标准<sup>1</sup> | 150 |
| 高可用性端口，标准版 | 每个内部前端1个 |

<sup>1</sup>限制是最多 150 种资源，采用独立虚拟机资源、可用性集资源和虚拟机规模集资源的任意组合。

#### <a name="virtual-networking-limits-classic"></a>以下限制仅适用于通过每个订阅的**经典**部署模型进行管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| Resource | 默认限制 | 最大限制 |
| --- | --- | --- |
| 虚拟网络 |100 |100 |
| 本地网络站点 |20 |50 |
| 每个虚拟网络的 DNS 服务器数 |20 |20 |
| 每个虚拟网络的专用 IP 地址数 |4,096 |4,096 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |如果 NIC 至少有两个，则为 500,000（至多 1,000,000）。 |如果 NIC 至少有两个，则为 500,000（至多 1,000,000）。 |
| 网络安全组 (NSG) |200 |200 |
| 每个 NSG 的 NSG 规则数 |1,000 |1,000 |
| 用户定义路由表数 |200 |200 |
| 每个路由表的用户定义的路由数 |400 |400 |
| 公共 IP 地址 (动态) |500 |500 |
| 保留的公共 IP 地址 |500 |500 |
| 每个部署的公共 VIP |5 |联系技术支持 |
| 每个部署的专用 VIP（内部负载均衡） |1 |1 |
| 终结点访问控制列表 (ACL) |50 |50 |
