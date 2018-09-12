---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f2d7aba05fc01c5a4dcdb123f25242c4e4a72578
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "43426416"
---
>[!NOTE]
>从 2018 年 7 月 1 日开始，Azure VPN 网关将不再支持 TLS 1.0 和 1.1。 VPN 网关将仅支持 TLS 1.2。 若要维持支持，请参阅[更新以支持 TLS1.2](#tls1)。

此外，TLS 也将于 2018 年 7 月 1 日起弃用以下旧算法：

* RC4 (Rivest Cipher 4)
* DES（数据加密算法）
* 3DES（三重数据加密算法）
* MD5（消息摘要 5）

### <a name="tls1"></a>如何在 Windows 7 和 Windows 8.1 中启用对 TLS 1.2 的支持？

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
