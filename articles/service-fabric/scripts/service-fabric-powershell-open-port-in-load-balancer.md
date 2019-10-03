---
title: Azure PowerShell 脚本示例 - 打开负载均衡器中的应用程序端口 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 打开 Azure 负载均衡器中 Service Fabric 应用程序的端口。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 05/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8e6a0b7f273df96cff5cc9ce1081ddc59eb49382
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035545"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>打开 Azure 负载均衡器中的应用程序端口

正在 Azure 中运行的 Service Fabric 应用程序位于 Azure 负载均衡器后。 此示例脚本将打开 Azure 负载均衡器中的端口，以便 Service Fabric 应用程序可与外部客户端通信。 根据需要自定义参数。 如果群集位于网络安全组中，请同时[添加入站网络安全组规则](service-fabric-powershell-add-nsg-rule.md)，以允许入站通信流流入。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要时，使用 [Service Fabric SDK](../service-fabric-get-started.md) 安装 Service Fabric PowerShell 模块。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 获取 Azure 资源。  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | 获取 Azure 负载均衡器。 |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | 向负载均衡器添加探测配置。|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | 获取负载均衡器的探测配置。 |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | 向负载均衡器添加规则配置。 |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | 设置负载均衡器的目标状态。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Powershell 示例。
