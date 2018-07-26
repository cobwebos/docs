---
title: include 文件
description: include 文件
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 06/20/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 9ba9bc993832350f6b6ce1c642e2dc852731b6f0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39029997"
---
<a name="virtual-networking-limits-classic"></a>以下限制仅适用于每个订阅通过经典部署模型托管的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 虚拟网络 |50 |100 |
| 本地网络站点 |20 |联系支持人员 |
| 每个虚拟网络的 DNS 服务器数 |20 |100 |
| 每个虚拟网络的专用 IP 地址数 |4096 |4096 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |500K |500K |
| 网络安全组 (NSG) |100 |200 |
| 每个 NSG 的 NSG 规则数 |200 |1000 |
| 用户定义路由表数 |100 |200 |
| 每个路由表的用户定义的路由数 |100 |400 |
| 公共 IP 地址 (动态) |5 |联系支持人员 |
| 保留的公共 IP 地址 |20 |联系支持人员 |
| 每个部署的公共 VIP |5 |联系支持人员 |
| 每个部署的私有 VIP (ILB) |1 |1 |
| 终结点访问控制列表 (ACL) |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>网络限制 - Azure 资源管理器
以下限制仅适用于每个订阅按区域通过 Azure 资源管理器管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)。

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 虚拟网络 |50 |1000 |
| 每个虚拟网络的子网数 |1000 |10000 |
| 每个虚拟网络的虚拟网络对等互连数 |50** |100 |
| 每个虚拟网络的 DNS 服务器数 |9 |25 |
| 每个虚拟网络的专用 IP 地址数 |16384** |16384 |
| 每个网络接口的专用 IP 地址数 |256 |256 |
| 虚拟机或角色实例的单 NIC 并发 TCP 或 UDP 流数 |500K |500K |
| 网络接口 (NIC) |24000** |24000 |
| 网络安全组 (NSG) |100 |5000 |
| 每个 NSG 的 NSG 规则数 |1000** |1000 |
| 为安全组中的源或目标指定的 IP 地址和范围数 |2000 |4000 |
| 应用程序安全组 |500 |3000 |
| 每个 IP 配置和每个 NIC 的应用程序安全组数 |10 |20 |
| 每个应用程序安全组的 IP 配置数 |1000 |4000 |
| 可在网络安全组的所有安全规则中指定的应用程序安全组数 |50 |100 |
| 用户定义路由表数 |100 |200 |
| 每个路由表的用户定义的路由数 |400** |400 |
| 公共 IP 地址数 - 动态 |（基本）60 |联系支持人员 |
| 公共 IP 地址数 - 静态 |（基本）20 |联系支持人员 |
| 公共 IP 地址数 - 静态 |（标准）20 |联系支持人员 |
| 每个 VPN 网关的点到站点根证书 |20 |20 |

**这些更新的默认限制适用于以前未通过客户支持提高这些上限的订阅。 如果你过去通过客户支持提高了这些上限，并且希望将其更新为新的默认值，请[免费建立联机客户支持请求](../articles/azure-resource-manager/resource-manager-quota-errors.md)

#### <a name="load-balancer"></a>负载均衡器限制
以下限制仅适用于每个订阅按区域通过 Azure 资源管理器管理的网络资源。 了解如何[针对订阅限制查看当前资源使用情况](../articles/networking/check-usage-against-limits.md)

| 资源 | 默认限制 | 最大限制 |
| --- | --- | --- |
| 负载均衡器 | 100 | 1000 |
| 每个资源的规则数，基本 | 150 | 250 |
| 每个资源的规则数，标准 | 1250 | 1500 |
| 每个 IP 配置的规则数 | 299 |299 |
| 前端 IP 配置数，基本 | 10 | 200 |
| 前端 IP 配置数，标准 | 10 | 600 |
| 后端池数，基本 | 100，单个可用性集 | 100，单个可用性集 |
| 后端池数，标准 | 1000，单个 VNet | 1000，单个 VNet |
| 每个负载均衡器的后端资源数，标准 &ast; | 50 | 150 |
| HA 端口数，标准 | 每个内部前端 1 个 | 每个内部前端 1 个 |

&ast; 多达 150 个资源，独立虚拟机、可用性集和虚拟机规模集可任意组合。

如果需要在默认值的基础上提高限制，请[与支持人员联系](../articles/azure-supportability/resource-manager-core-quotas-request.md )。

