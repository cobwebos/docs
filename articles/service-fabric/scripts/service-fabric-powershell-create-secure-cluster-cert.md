---
title: "Azure PowerShell 脚本示例 - 创建 Service Fabric 群集 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 创建 Service Fabric 群集。"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: a6d44d810fa9f9a4660afbaa4cbb0af37c5e0d13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集

此示例脚本将创建一个 Service Fabric 群集（使用 X.509 证书保护的由五个节点组成的群集）。  该命令将创建一个自签名证书，并将其上传到新的 Key Vault。 该证书也会复制到本地目录。  设置 *-OS* 参数可选择群集节点上运行的 Windows 或 Linux 的版本。  根据需要自定义参数。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Login-AzureRmAccount` 创建与 Azure 的连接。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、群集以及所有相关资源。

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | 新建 Service Fabric 群集。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Azure Powershell 示例。
