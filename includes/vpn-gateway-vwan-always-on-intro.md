---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/12/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4ea97e2dbee87f7ab129c4295276c9024c0212c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117226"
---
Windows 10 VPN 客户端 Always On 的一项新功能是能够维护 VPN 连接。 有了 Always On，有效的 VPN 配置文件就能根据触发因素（例如用户登录、网络状态更改或设备屏幕活动状态）自动建立连接并保持连接。

您可以将 Windows 10 始终打开的网关建立到 Azure 的持久用户隧道和设备隧道。 本文介绍如何配置 Always On VPN 用户隧道。

Always On VPN 连接包括下述两种隧道类型之一：

* **设备隧道**：在用户登录到设备之前连接到指定的 VPN 服务器。 预登录连接方案和设备管理使用设备隧道。

* **用户隧道**：仅在用户登录到设备后连接。 可以使用用户隧道通过 VPN 服务器访问组织资源。

设备隧道和用户隧道的运行独立于其 VPN 配置文件。 它们可以同时连接，在适当的情况下可以使用不同的身份验证方法和其他 VPN 配置设置。
