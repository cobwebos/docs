---
title: 在Powershell 中添加网络安全组规则
description: Azure PowerShell 脚本示例 - 添加网络安全组以允许特定端口上的入站流量。
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
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 167fa74b04af7c086c2c95b0fdd56e5932483080
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076203"
---
# <a name="add-an-inbound-network-security-group-rule"></a>添加入站网络安全组规则

本示例脚本创建网络安全组规则，以允许端口 8081 上的入站流量。  该脚本获取网络安全组、创建新的网络安全配置规则，并更新网络安全组。 根据需要自定义参数。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/)中的说明安装 Azure PowerShell。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 获取 `Microsoft.Network/networkSecurityGroups` 资源。 |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| 按名称获取网络安全组。|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| 将网络安全规则配置添加到网络安全组。 |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| 设置网络安全组的目标状态。|

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/)。
