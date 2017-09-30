---
title: "Azure PowerShell 脚本示例 - 升级 Service Fabric 应用程序 | Microsoft Docs"
description: "Azure PowerShell 脚本示例 - 升级 Service Fabric 应用程序。"
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
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: e86b1bea4dab98292053571f72962e7aa3f8cfa1
ms.contentlocale: zh-cn
ms.lasthandoff: 09/02/2017

---

# <a name="upgrade-a-service-fabric-application"></a>升级 Service Fabric 应用程序

此示例脚本可将正在运行的 Service Fabric 应用程序实例升级为版本 1.3.0。 该脚本可将新的应用程序包复制到群集映像存储、注册应用程序类型、启动受监控的升级，并不断检查升级状态，直到升级完成或回滚。 根据需要自定义参数。 

必要时，使用 [Service Fabric SDK](../service-fabric-get-started.md) 安装 Service Fabric PowerShell 模块。 

## <a name="sample-script"></a>示例脚本

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | 获取 Service Fabric 群集中的所有应用程序或特定应用程序。  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | 获取 Service Fabric 应用程序的升级状态。 |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | 获取在 Service Fabric 群集上注册的 Service Fabric 应用程序类型。 |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | 注销 Service Fabric 应用程序类型。  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | 将 Service Fabric 应用程序包复制到映像存储。  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | 注册 Service Fabric 应用程序类型。 |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | 将 Service Fabric 应用程序升级为指定的应用程序类型版本。 |


## <a name="next-steps"></a>后续步骤

有关 Service Fabric PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/service-fabric/?view=azureservicefabricps)。

可以在 [Azure PowerShell 示例](../service-fabric-powershell-samples.md)中找到 Azure Service Fabric 的其他 Powershell 示例。

