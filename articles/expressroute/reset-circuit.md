---
title: 重置有故障的线路 - ExpressRoute：PowerShell：Azure | Microsoft Docs
description: 本文帮助你重置处于故障状态的 ExpressRoute 线路。
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: 0332f189feb1b266eac29c9ea8a7f3ed86d6207a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58116565"
---
# <a name="reset-a-failed-expressroute-circuit"></a>重置有故障的 ExpressRoute 线路

针对 ExpressRoute 线路执行的某个操作未成功完成时，该线路可能进入“故障”状态。 本文帮助你重置有故障的 Azure ExpressRoute 线路。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reset-a-circuit"></a>重置线路

1. 安装最新版本的 Azure 资源管理器 PowerShell cmdlet。 有关详细信息，请参阅[安装和配置 Azure PowerShell](/powershell/azure/install-az-ps)。

2. 使用提升的权限打开 PowerShell 控制台，并连接到帐户。 使用下面的示例来帮助连接：

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. 如果有多个 Azure 订阅，请查看该帐户的订阅。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. 指定要使用的订阅。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. 运行以下命令可重置处于故障状态的线路：

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

现在，该线路应已恢复正常。 如果线路仍处于故障状态，请向 [Microsoft 支持部门](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具支持票证。

## <a name="next-steps"></a>后续步骤

如果仍然存在问题，请通过 [Microsoft 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)开具一个支持票证。
