---
title: 在 Azure 中配置负载均衡器 TCP 重置和空闲超时
titleSuffix: Azure Load Balancer
description: 本文介绍如何配置 Azure 负载均衡器 TCP 空闲超时。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/09/2020
ms.author: allensu
ms.openlocfilehash: 26c4c01aaf6abe6b9c9ac6daf6836d7b660ba21e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649808"
---
# <a name="configure-tcp-idle-timeout-for-azure-load-balancer"></a>配置 Azure 负载均衡器的 TCP 空闲超时

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本文需要 Azure PowerShell 模块 5.4.1 或更高版本。 运行 `Get-Module -ListAvailable Az` 查找已安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-Az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 以创建与 Azure 的连接。

Azure 负载均衡器的空闲超时设置为4分钟到120分钟。 默认情况下，它设置为 4 分钟。 如果处于非活动状态的时间超过超时值，则不能保证在客户端和云服务之间保持 TCP 或 HTTP 会话。 详细了解 [TCP 空闲超时](load-balancer-tcp-reset.md)。

以下部分介绍如何更改公共 IP 和负载均衡器资源的空闲超时设置。


## <a name="configure-the-tcp-idle-timeout-for-your-public-ip"></a>配置公共 IP 的 TCP 空闲超时

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

`IdleTimeoutInMinutes` 是可选项。 如果未设置，默认超时为 4 分钟。 可接受的超时范围为4到120分钟。

## <a name="set-the-tcp-idle-timeout-on-rules"></a>设置规则的 TCP 空闲超时

若要为负载均衡器设置空闲超时，请在负载均衡规则上设置“IdleTimeoutInMinutes”。 例如：

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name "MyLoadBalancer" -ResourceGroup "MyResourceGroup"
$lb | Set-AzLoadBalancerRuleConfig -Name myLBrule -IdleTimeoutInMinutes 15
```

## <a name="next-steps"></a>后续步骤

[内部负载均衡器概述](load-balancer-internal-overview.md)

[开始配置面向 Internet 的负载均衡器](quickstart-load-balancer-standard-public-powershell.md)

[配置负载均衡器分发模式](load-balancer-distribution-mode.md)
