---
title: "使用 PowerShell 克隆 Web 应用"
description: "了解如何使用 PowerShell 将 Web 应用克隆到新的 Web 应用。"
services: app-service\web
documentationcenter: 
author: ahmedelnably
manager: stefsch
editor: 
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.openlocfilehash: 30817a1a6a8079e7a896305ab0b59e48fad4d644
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>使用 PowerShell 克隆 Azure 应用服务应用
在发行的 Microsoft Azure PowerShell 版本 1.1.0 中，为 `New-AzureRMWebApp` 添加了新选项，可让用户将现有 Web 应用克隆到不同区域或相同区域中的新建应用。 使用此选项，客户可跨不同区域快速轻松地部署多个应用。

应用克隆目前仅支持高级层应用服务计划。 新功能使用与 Web 应用备份功能相同的限制，具体请参阅[在 Azure 应用服务中备份 Web 应用](web-sites-backup.md)。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="cloning-an-existing-app"></a>克隆现有应用
场景：用户想要将位于美国中南部区域的现有 Web 应用内容克隆到位于美国中北部区域的新 Web 应用。 结合 `-SourceWebApp` 选项使用 Azure 资源管理器版本的 PowerShell cmdlet 来创建新的 Web 应用，即可实现此目的。

如果知道包含源 Web 应用的资源组名称，就可以使用以下 PowerShell 命令来获取源 Web 应用的信息（在本例中，该应用名为 `source-webapp`）：

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

要创建新的应用服务计划，可按以下示例中所示来使用 `New-AzureRmAppServicePlan` 命令

```PowerShell
New-AzureRmAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

使用 `New-AzureRmWebApp` 命令，可在美国中北部区域内创建新的 Web 应用，并将其绑定到现有高级层应用服务计划。 此外，还可以使用相同的资源组作为源 Web 应用，或定义新的资源组，如以下命令所示：

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

要克隆现有 Web 应用（包括所有关联的部署槽位），需要使用 `IncludeSourceWebAppSlots` 参数。 以下 PowerShell 命令演示如何在 `New-AzureRmWebApp` 命令中使用该参数：

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

要在同一区域中克隆现有 Web 应用，需要在同一区域中创建新资源组和新的应用服务计划，然后使用以下 PowerShell 命令来克隆 Web 应用

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>将现有应用克隆到应用服务环境
场景：用户想要将位于美国中南部区域的现有 Web 应用内容克隆到现有应用服务环境 (ASE) 的新 Web 应用中。

如果知道包含源 Web 应用的资源组名称，就可以使用以下 PowerShell 命令来获取源 Web 应用的信息（在本例中，该应用名为 `source-webapp`）：

```PowerShell
$srcapp = Get-AzureRmWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

如果知道 ASE 的名称和 ASE 所属的资源组名称，可在现有 ASE 中创建新的 Web 应用，如以下命令所示：

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

由于使用了旧版本，需要提供 `Location` 参数，但在 ASE 中创建应用时，可忽略该参数。 

## <a name="cloning-an-existing-app-slot"></a>克隆现有的应用槽
场景：用户想要将现有 Web 应用槽克隆到新的 Web 应用或新的 Web 应用槽。 新的 Web 应用可与原始 Web 应用槽位于相同或不同的区域中。

如果知道包含源 Web 应用的资源组名称，可使用以下 PowerShell 命令来获取绑定到 Web 应用 `source-webapp` 的源 Web 应用槽的信息（在本例中，该应用槽名为 `source-webappslot`）：

```PowerShell
$srcappslot = Get-AzureRmWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-webapp -Slot source-webappslot
```

以下命令演示如何在新 Web 应用中创建源 Web 应用的克隆：

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>在克隆应用时配置流量管理器
通过创建多区域 Web 应用和配置 Azure 流量管理器来将流量路由到所有这些 Web 应用是一个重要的方案，可确保客户应用的高可用性。 克隆现有 Web 应用时，可以选择将两个 Web 应用都连接到新的或现有的流量管理器配置文件。 仅支持 Azure 资源管理器版本的流量管理器。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>在克隆应用时创建新的流量管理器配置文件
场景：用户想要将 Web 应用克隆到另一个区域，同时配置包含两个 Web 应用的 Azure 资源管理器流量管理器配置文件。 以下命令演示如何在新 Web 应用中创建源 Web 应用的克隆，同时配置新的流量管理器配置文件：

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-web-app-to-an-existing-traffic-manager-profile"></a>将新克隆的 Web 应用添加到现有的流量管理器配置文件
场景：用户已有一个 Azure 资源管理器流量管理器配置文件，现在想要将两个 Web 应用添加为终结点。 要执行此操作，首先需要组合现有流量管理器配置文件 ID。 需要订阅 ID、资源组名称和现有流量管理器配置文件名称。

```PowerShell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

获取流量管理器 ID 之后，根据以下命令演示在新 Web 应用中创建源 Web 应用的克隆，同时将它们添加到现有流量管理器配置文件：

```PowerShell
$destapp = New-AzureRmWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>当前限制
此功能目前处于预览状态，以后会陆续添加新功能。 下面是当前版本应用克隆的已知限制：

* 不会克隆自动缩放设置
* 不会克隆备份计划设置
* 不会克隆 VNET 设置
* 不会自动在目标 Web 应用上设置 App Insights
* 不会克隆简易身份验证设置
* 不会克隆 Kudu 扩展
* 不会克隆 TiP 规则
* 不会克隆数据库内容
* 如果克隆到不同的缩放单位，出站 IP 地址会更改

### <a name="references"></a>参考
* [Web 应用克隆](app-service-web-app-cloning.md)
* [在 Azure 应用服务中备份 Web 应用](web-sites-backup.md)
* [Azure 流量管理器预览版对 Azure 资源管理器的支持](../traffic-manager/traffic-manager-powershell-arm.md)
* [应用服务环境简介](environment/intro.md)
* [将 Azure PowerShell 与 Azure 资源管理器配合使用](../azure-resource-manager/powershell-azure-resource-manager.md)

