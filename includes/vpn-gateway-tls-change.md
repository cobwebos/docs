---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0377548a3b026657ae3282801523b1c0c6731265
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236668"
---
从 2018 年 7 月 1 日开始，Azure VPN 网关将不再支持 TLS 1.0 和 1.1。 VPN 网关将仅支持 TLS 1.2。 仅点到站点连接会受到影响；站点到站点连接不受影响。 如果在 Windows 10 客户端上点到站点 VPN 使用的是 TLS，则无需进行任何操作。 如果在 Windows 7 和 Windows 8 客户端上使用 TLS 建立点到站点连接，请参阅 [VPN 网关常见问题解答](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)，了解更新说明。