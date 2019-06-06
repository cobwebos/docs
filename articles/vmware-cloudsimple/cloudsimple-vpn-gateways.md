---
title: 通过 CloudSimple-Azure 的 VMware 解决方案中的 VPN 网关
description: 了解 CloudSimple 站点到站点 VPN 和点到站点 VPN 概念
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b2630614e549181f4dd2f4e79871c4594d09201
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496867"
---
# <a name="vpn-gateways-overview"></a>VPN 网关概述

VPN 网关用于在本地位置或通过公共 Internet 的计算机的 CloudSimple 区域网络之间发送加密的流量。  每个区域只能有一个 VPN 网关。 但是，可以创建连接到相同 VPN 网关的多个连接。 与同一个 VPN 网关建立多个连接时，所有 VPN 隧道共享可用的网关带宽。

CloudSimple 提供了两种类型的 VPN 网关：

* 站点到站点 VPN 网关
* 点到站点 VPN 网关

## <a name="site-to-site-vpn-gateway"></a>站点到站点 VPN 网关

站点到站点 VPN 网关用于 CloudSimple 区域网络和本地数据中心之间发送加密的流量。 使用此连接定义的子网/CIDR 范围，你的本地网络和 CloudSimple 区域网络之间的网络流量。

VPN 网关，可使用从本地对私有云的服务和服务在私有云，从本地网络上。  CloudSimple 提供基于策略的 VPN 服务器建立从本地网络连接。

站点到站点 VPN 的用例包括：

* 私有云 vCenter 从任何工作站的本地网络中的可访问性。
* 将你的本地 Active Directory 用作 vCenter 标识源。
* 方便的传输 VM 模板、 Iso 和和其他文件从你的本地资源到私有云 vCenter。
* 从你的本地网络在私有云上运行的工作负荷的可访问性。

![站点到站点 VPN 连接拓扑](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>加密的参数

站点到站点 VPN 连接使用以下默认加密参数建立安全连接。  当你创建从本地 VPN 设备的连接时，必须匹配参数。

站点到站点 VPN 连接使用以下默认加密参数建立安全连接。  当你创建从本地 VPN 设备的连接时，使用任何本地 VPN 网关支持以下参数。

#### <a name="phase-1-proposals"></a>第 1 阶段征求建议书

| 参数 | 方案 1 | 方案 2 | 方案 3 |
|-----------|------------|------------|------------|
| SDK 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman 组 （DH 组） | 第 | 1 | 第 |
| 生存时间 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |


#### <a name="phase-2-proposals"></a>第 2 阶段征求建议书 

| 参数 | 方案 1 | 方案 2 | 方案 3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| 完美正向保密组 （PFS 组） | 无 | 无 | 无 |
| 生存时间 | 1800 秒 | 1800 秒 | 1800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>点到站点 VPN 网关

点到站点 VPN 用于 CloudSimple 区域网络和客户端计算机之间发送加密的流量。  点到站点 VPN 是访问你的私有云的网络，包括你的私有云 vCenter 和工作负荷 Vm 的最简单方法。  如果您要远程连接到私有云，请使用点到站点 VPN 连接。

## <a name="next-steps"></a>后续步骤

* [设置 VPN 网关](https://docs.azure.cloudsimple.com/vpn-gateway/)