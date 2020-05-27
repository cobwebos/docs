---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67172830"
---
如果使用的是资源管理器部署模型，则可更改到新式网关 SKU。 当从旧式网关 SKU 更改到新式 SKU 时，需删除现有 VPN 网关并创建新的 VPN 网关。

工作流程：

1. 删除到虚拟网关的任何连接。
2. 删除旧的 VPN 网关。
3. 创建新的 VPN 网关。
4. 使用新的 VPN 网关 IP 地址更新本地 VPN 设备（适用于站点到站点连接）。
5. 更新将连接到本网关的任何 VNet 到 VNet 本地网关的网关 IP 地址值。
6. 下载适用于 P2S 客户端（通过此 VPN 网关连接到虚拟网络）的新客户端 VPN 配置包。
7. 重新创建到虚拟网关的连接。

注意事项：

* 若要更改到新式 SKU，VPN 网关必须处于资源管理器部署模型中。
* 如果有经典的 VPN 网关，必须对该网关继续使用早期的旧式 SKU，但可以在旧式 SKU 之间重设网关大小。 不能更改为新式 SKU。
* 当从旧式 SKU 更改到新式 SKU 时，连接将中断。
* 更改为新网关 SKU 时，VPN 网关的公共 IP 地址将更改。 即使指定以前使用的同一公共 IP 地址对象，也会出现这种情况。