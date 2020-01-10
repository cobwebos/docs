---
title: 在 Azure 中配置负载均衡器 TCP 空闲超时
titleSuffix: Azure Load Balancer
description: 本文介绍如何配置 Azure 负载均衡器 TCP 空闲超时。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 565707b0e081a495f01f369125584038981b4ae8
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834652"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>为 Azure 负载均衡器配置 TCP 空闲超时设置

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

## <a name="tcp-idle-timeout"></a>TCP 空闲超时
在默认配置中，Azure 负载均衡器的空闲超时设置为 4 分钟。 如果处于非活动状态的时间超过超时值，则不能保证在客户端和云服务之间保持 TCP 或 HTTP 会话。

当连接关闭时，客户端应用程序可能收到以下错误消息：“基础连接已关闭: 应保持连接状态的连接已由服务器关闭”。

常见的做法是使用 TCP 保持连接状态。 这种做法可以将连接状态保持更长时间。 有关详细信息，请参阅 [.NET 示例](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 在启用保持连接状态的情况下，在连接处于非活动状态时发送数据包。 保持活动状态的数据包可确保不会达到空闲超时值，并且会长时间维护连接。

此设置仅适用于入站连接。 若要避免丢失连接，请将 TCP keep-alive 配置为小于空闲超时设置，或者增加空闲超时值。 为了支持这些方案，添加了对可配置空闲超时值的支持。 现在，可以将空闲超时的持续时间设置为 4 到 30 分钟。

TCP keep-alive 适用于电池寿命不是限制的情况。 不建议将其用于移动应用程序。 在移动应用程序中使用 TCP 保持连接状态可能会加快设备电池的耗尽速度。

![TCP 超时](./media/load-balancer-tcp-idle-timeout/image1.png)

以下部分介绍如何更改公共 IP 和负载均衡器资源的空闲超时设置。

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>将实例级公共 IP 的 TCP 超时值配置为 15 分钟

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` 是可选项。 如果未设置，则默认超时为4分钟。 可接受的超时范围为 4 到 30 分钟。

## <a name="set-the-tcp-timeout-on-a-load-balanced-rule-to-15-minutes"></a>将负载平衡规则的 TCP 超时设置为15分钟

若要为负载均衡器设置空闲超时，请在负载平衡规则上设置 "IdleTimeoutInMinutes"。 例如：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```
## <a name="next-steps"></a>后续步骤

[内部负载均衡器概述](load-balancer-internal-overview.md)

[开始配置面向 Internet 的负载均衡器](load-balancer-get-started-internet-arm-ps.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)
