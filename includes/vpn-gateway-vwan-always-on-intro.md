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
ms.openlocfilehash: 2783c828f96eeb3539e2266eaf1d6d590a6af4c4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370949"
---
Windows 10 VPN 客户端 Always On 的一项新功能是能够维护 VPN 连接。 对于 Always On，活动的 VPN 配置文件可以自动连接，并根据触发器（例如用户登录、网络状态更改或设备屏幕活动）保持连接。

可以通过 Windows 10 Always On 使用 Azure 虚拟网络网关，建立到 Azure 的持久用户隧道和设备隧道。 本文可帮助你配置 Always On VPN 用户隧道。

Always On VPN 连接包括两种类型的隧道：

* **设备隧道**：在用户登录到设备之前连接到指定的 VPN 服务器。 预登录连接方案和设备管理使用设备隧道。

* **用户隧道**：仅在用户登录到设备后进行连接。 通过使用用户隧道，你可以通过 VPN 服务器访问组织资源。

设备隧道和用户隧道独立于其 VPN 配置文件运行。 它们可以同时连接，并且可以根据需要使用不同的身份验证方法和其他 VPN 配置设置。
