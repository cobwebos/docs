---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67172837"
---
下表列出了基于策略和基于路由的 VPN 网关的要求。 此表适用于 Resource Manager 与经典部署模型。 对于经典模型，基于策略的 VPN 网关与静态网关相同，基于路由的网关与动态网关相同。

|  | **基于策略的基本 VPN 网关** | **基于路由的基本 VPN 网关** | **基于路由的标准 VPN 网关** | **基于路由的高性能 VPN 网关** |
| --- | --- | --- | --- | --- |
| **站点到站点连接 (S2S)** |基于策略的 VPN 配置 |基于路由的 VPN 配置 |基于路由的 VPN 配置 |基于路由的 VPN 配置 |
| **点到站点连接 (P2S)** |不支持 |支持（可与 S2S 共存） |支持（可与 S2S 共存） |支持（可与 S2S 共存） |
| **身份验证方法** |预共享密钥 |S2S 连接的预共享密钥，P2S 连接的证书 |S2S 连接的预共享密钥，P2S 连接的证书 |S2S 连接的预共享密钥，P2S 连接的证书 |
| **S2S 连接的最大数目** |1 |10 |10 |30 |
| **P2S 连接的最大数目** |不支持 |128 |128 |128 |
| **活动路由支持 (BGP)** (*) |不支持 |不支持 |支持 |支持 |

  (*) 经典部署模型不支持 BGP。
