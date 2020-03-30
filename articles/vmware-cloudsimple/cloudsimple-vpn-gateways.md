---
title: Azure VMware 解决方案（按云简单 - VPN 网关）
description: 了解云简单站点到站点 VPN 和点对点 VPN 概念
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 662fa342b3a18f726b418c496ff3fda937445301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024851"
---
# <a name="vpn-gateways-overview"></a>VPN 网关概述

VPN 网关用于在本地位置的 CloudSimple 区域网络之间或通过公共 Internet 的计算机发送加密流量。  每个区域可以有一个 VPN 网关，可以支持多个连接。 与同一个 VPN 网关建立多个连接时，所有 VPN 隧道共享可用的网关带宽。

CloudSimple 提供两种类型的 VPN 网关：

* 站点到站点 VPN 网关
* 点对点 VPN 网关

## <a name="site-to-site-vpn-gateway"></a>站点到站点 VPN 网关

站点到站点 VPN 网关用于在 CloudSimple 区域网络和本地数据中心之间发送加密流量。 使用此连接可以定义子网/CIDR 范围，用于本地网络和云简单区域网络之间的网络流量。

VPN 网关允许您使用来自私有云的本地服务，以及从本地网络使用私有云上的服务。  CloudSimple 提供了一个基于策略的 VPN 服务器，用于从本地网络建立连接。

站点到站点 VPN 的用例：

* 从本地网络中的任何工作站访问私有云 vCenter。
* 将本地活动目录用作 vCenter 标识源。
* 方便地将 VM 模板、ISO 和其他文件从本地资源传输到私有云 vCenter。
* 从本地网络访问在私有云上运行的工作负载。

![站点到站点 VPN 连接拓扑](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>加密参数

站点到站点 VPN 连接使用以下默认加密参数来建立安全连接。  从本地 VPN 设备创建连接时，请使用本地 VPN 网关支持的以下任何参数。

#### <a name="phase-1-proposals"></a>第一阶段提案

| 参数 | 提案1 | 提案2 | 提案3 |
|-----------|------------|------------|------------|
| SDK 版本 | IKEv1 | IKEv1 | IKEv1 |
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| 迪菲·赫尔曼集团（DH集团） | 2 | 2 | 2 |
| 生存时间 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>第二阶段提案

| 参数 | 提案1 | 提案2 | 提案3 |
|-----------|------------|------------|------------|
| 加密 | AES 128 | AES 256 | AES 256 |
| 哈希算法| SHA 256 | SHA 256 | SHA 1 |
| 完美前进保密集团（PFS集团） | 无 | 无 | 无 |
| 生存时间 | 1，800 秒 | 1，800 秒 | 1，800 秒 |
| 数据大小 | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> 在 VPN 设备上将 TCP MSS 夹紧设置为 1200。 或者，如果您的 VPN 设备不支持 MSS 夹紧，则可以将隧道接口上的 MTU 设置为 1240 字节。

## <a name="point-to-site-vpn-gateway"></a>点对点 VPN 网关

点对点 VPN 用于在 CloudSimple 区域网络和客户端计算机之间发送加密流量。  点对点 VPN 是访问私有云网络的最简单方法，包括私有云 vCenter 和工作负载 VM。  如果您远程连接到私有云，请使用点对点 VPN 连接。

## <a name="next-steps"></a>后续步骤

* [设置 VPN 网关](vpn-gateway.md)
