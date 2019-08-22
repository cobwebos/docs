---
title: Azure VMware 解决方案 (按 CloudSimple-VPN 网关)
description: 了解 CloudSimple 站点到站点 VPN 和点到站点 VPN 概念
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 88c27b920817da5edc2cefe780903c2b94695807
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877641"
---
# <a name="vpn-gateways-overview"></a>VPN 网关概述

VPN 网关用于在本地位置的 CloudSimple 区域网络或通过公共 internet 的计算机之间发送加密流量。  每个区域可以有一个 VPN 网关, 该网关可以支持多个连接。 与同一个 VPN 网关建立多个连接时，所有 VPN 隧道共享可用的网关带宽。

CloudSimple 提供了两种类型的 VPN 网关:

* 站点到站点 VPN 网关
* 点到站点 VPN 网关

## <a name="site-to-site-vpn-gateway"></a>站点到站点 VPN 网关

站点到站点 VPN 网关用于在 CloudSimple 区域网络和本地数据中心之间发送加密流量。 使用此连接可以定义本地网络与 CloudSimple 区域网络之间的网络流量的子网/CIDR 范围。

使用 VPN 网关, 你可以在私有云上使用本地服务, 也可以从本地网络使用私有云上的服务。  CloudSimple 提供基于策略的 VPN 服务器, 用于从本地网络建立连接。

站点到站点 VPN 的用例:

* 本地网络中任何工作站的私有云 vCenter 的可访问性。
* 使用本地 Active Directory 作为 vCenter 标识源。
* 方便地将 VM 模板、Iso 和其他文件从本地资源传输到私有云 vCenter。
* 从本地网络在私有云上运行的工作负荷的可访问性。

![站点到站点 VPN 连接拓扑](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>加密参数

站点到站点 VPN 连接使用以下默认加密参数建立安全连接。  当你从本地 VPN 设备创建连接时, 请使用你的本地 VPN 网关支持的以下任何参数。

#### <a name="phase-1-proposals"></a>阶段1提议

| 参数 | 建议1 | 建议2 | 建议3 |
|-----------|------------|------------|------------|
| SDK 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| Diffie-hellman 组 (DH 组) | 2 | 2 | 2 |
| 生命时间 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>阶段2方案

| 参数 | 建议1 | 建议2 | 建议3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| 完全向前保密组 (PFS 组) | None | 无 | 无 |
| 生命时间 | 1800秒 | 1800秒 | 1800秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>点到站点 VPN 网关

点到站点 VPN 用于在 CloudSimple 区域网络和客户端计算机之间发送加密流量。  点到站点 VPN 是访问私有云网络 (包括私有云 vCenter 和工作负荷 Vm) 的最简单方法。  如果要远程连接到私有云, 请使用点到站点 VPN 连接。

## <a name="next-steps"></a>后续步骤

* [设置 VPN 网关](vpn-gateway.md)
