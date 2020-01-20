---
title: 根据限制检查 Azure 资源使用情况 | Microsoft Docs
description: 了解如何根据 Azure 订阅限制检查 Azure 资源使用情况。
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: f59b688b2ce41985d69e800d6d1f6c6d7ce5e0d4
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278334"
---
# <a name="check-resource-usage-against-limits"></a>根据限制检查资源使用情况

本文介绍如何查看已在订阅中部署的每种网络资源类型的数量，以及[订阅限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)是什么。 根据限制查看资源使用情况的功能有助于跟踪当前使用情况，并为将来使用做出计划。 可以使用 [Azure 门户](#azure-portal)、[PowerShell](#powershell) 或 [Azure CLI](#azure-cli) 跟踪使用情况。

## <a name="azure-portal"></a>Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户左上角选择“所有服务”。
3. 在“筛选器”框中，键入“订阅”。 当“订阅”出现在搜索结果中时，请选择它。
4. 选择要查看其使用情况信息的订阅的名称。
5. 在“设置”下选择“使用情况 + 配额”。
6. 可以选择以下选项：
   - **资源类型**：可选择所有资源类型，也可选择要查看的特定资源类型。
   - **提供程序**：可选择所有资源提供程序，也可选择“计算”、“网络”或“存储”。
   - **位置**：可以选择所有 Azure 位置，也可选择特定位置。
   - 可以选择显示所有资源，也可只显示至少部署了一个资源的资源。

     下图中的示例显示了在美国东部至少部署了一个资源的所有网络资源：

       ![查看使用情况数据](./media/check-usage-against-limits/view-usage.png)

     可以通过选择列标题来对列进行排序。 显示的限制是订阅的限制。 如果需要增加默认限制，请选择“请求增加”，然后完成并提交支持请求。 所有资源都具有 Azure [限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits)中列出的最大限制。 如果当前限制已达到最大数量，则不能增加限制。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

可以在 [Azure Cloud Shell](https://shell.azure.com/powershell) 中运行以下命令，或者在计算机上运行 PowerShell。 Azure Cloud Shell 是免费的交互式 shell。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 如果从计算机运行 PowerShell，则需要 Azure PowerShell 模块1.0.0 或更高版本。 在计算机上运行 `Get-Module -ListAvailable Az`，找到已安装的版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 以登录到 Azure。

查看使用[AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage)的限制的使用情况。 以下示例获取在“美国东部”位置至少部署了一个资源的资源的使用情况：

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

你会收到格式与以下示例输出相同的输出：

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

如果使用 Azure 命令行接口 (CLI) 命令来完成本文中的任务，请运行 [Azure Cloud Shell](https://shell.azure.com/bash) 中的命令，或从计算机运行 CLI。 本文需要 Azure CLI 2.0.32 或更高版本。 运行 `az --version` 查找已安装的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 如果在本地运行 Azure CLI，则还需运行 `az login` 以登录到 Azure。

使用 [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages) 根据限制查看使用情况。 以下示例获取“美国东部”位置的资源使用情况：

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

你会收到格式与以下示例输出相同的输出：

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
