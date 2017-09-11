---
title: "Azure PowerShell 脚本示例 - 从群集中删除应用程序 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 从 Service Fabric 群集中删除应用程序。"
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 06/20/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 951c9a014f587ce53450dd705fc478da625b1e6b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/15/2017

---

# <a name="remove-an-application-from-a-service-fabric-cluster"></a>从 Service Fabric 群集中删除应用程序

此示例脚本将删除正在运行的 Service Fabric 应用程序实例，从群集中注销应用程序类型和版本，并从群集映像存储区中删除应用程序包。  删除应用程序实例时也会删除与该应用程序关联的所有正在运行的服务实例。 根据需要自定义参数。 

必要时，使用 [Service Fabric SDK](../service-fabric-get-started.md) 安装 Service Fabric PowerShell 模块。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "从群集中删除应用程序")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | 从群集中删除正在运行的 Service Fabric 应用程序实例。  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | 从群集中注销 Service Fabric 应用程序类型和版本。 |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | 从映像存储区中删除 Service Fabric 应用程序包。|

## <a name="next-steps"></a>后续步骤

有关 Service Fabric PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/service-fabric/?view=azureservicefabricps)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Powershell 示例。

