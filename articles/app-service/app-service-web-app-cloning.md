---
title: 使用 PowerShell 克隆应用
description: 了解如何使用 PowerShell 将“应用服务”应用克隆到新的应用。 涵盖各种克隆方案，包括流量管理器集成。
author: ahmedelnably
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: ccff07009d2f46f6d91b8e3c57158aa6ede3607e
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671434"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>使用 PowerShell 克隆 Azure 应用服务应用

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

在 Microsoft 发行的 Azure PowerShell 版本 1.1.0 中，为 `New-AzWebApp` 添加了新选项，可让用户将现有“应用服务”应用克隆到不同区域或相同区域中的新建应用。 使用此选项，客户可跨不同区域快速轻松地部署多个应用。

标准、高级、高级 V2 和独立应用服务计划支持应用克隆。 新功能使用与应用服务备份功能相同的限制，具体请参阅[在 Azure 应用服务中备份应用](manage-backup.md)。

## <a name="cloning-an-existing-app"></a>克隆现有应用
方案： "美国中南部" 区域中的现有应用，你想要将内容克隆到位于美国中北部区域的新应用。 结合 `-SourceWebApp` 选项使用 Azure 资源管理器版本的 PowerShell cmdlet 来创建新的应用，即可实现此目的。

如果知道包含源应用的资源组名称，就可以使用以下 PowerShell 命令来获取源应用的信息（在本例中，该应用名为 `source-webapp`）：

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

要创建新的应用服务计划，可按以下示例中所示来使用 `New-AzAppServicePlan` 命令

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

使用 `New-AzWebApp` 命令，可在美国中北部区域创建新应用，并将其绑定到现有应用服务计划。 此外，还可以使用相同的资源组作为源应用，或定义新的资源组，如以下命令所示：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

要克隆现有应用（包括所有关联的部署槽位），需要使用 `IncludeSourceWebAppSlots` 参数。  请注意，仅支持克隆包含其所有槽的整个应用程序的 `IncludeSourceWebAppSlots` 参数。 以下 PowerShell 命令演示如何在 `New-AzWebApp` 命令中使用该参数：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

要在同一区域中克隆现有应用，需要在同一区域中创建新资源组和新的应用服务计划，然后使用以下 PowerShell 命令来克隆应用：

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>将现有应用克隆到应用服务环境
方案： "美国中南部" 区域中的现有应用，你想要将内容克隆到现有应用服务环境（ASE）的新应用。

如果知道包含源应用的资源组名称，就可以使用以下 PowerShell 命令来获取源应用的信息（在本例中，该应用名为 `source-webapp`）：

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

如果知道 ASE 的名称和 ASE 所属的资源组名称，可在现有 ASE 中创建新的应用，如以下命令所示：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

由于使用了旧版本，需要提供 `Location` 参数，但在 ASE 中创建应用时，可忽略该参数。 

## <a name="cloning-an-existing-app-slot"></a>克隆现有的应用槽
方案：你要将应用的现有部署槽克隆到新应用或新槽。 新的应用可与原始应用槽位于相同或不同的区域。

如果知道包含源应用的资源组名称，就可以使用以下 PowerShell 命令来获取与 `source-app` 相关的源应用的槽位信息（在本例中，该应用名为 `source-appslot`）：

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

以下命令演示如何在新应用中创建源应用的克隆：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>在克隆应用时配置流量管理器
通过创建多区域应用和配置 Azure 流量管理器来将流量路由到所有这些应用是一个重要的方案，可确保客户应用的高可用性。 克隆现有应用时，可以选择将两个应用都连接到新的或现有的流量管理器配置文件。 仅支持 Azure 资源管理器版本的流量管理器。

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>在克隆应用时创建新的流量管理器配置文件
方案：你要将应用克隆到另一个区域，同时配置包含两个应用的 Azure 资源管理器流量管理器配置文件。 以下命令演示如何在新应用中创建源应用的克隆，同时配置新的流量管理器配置文件：

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>将新克隆的应用添加到现有的流量管理器配置文件
方案：你已有 Azure 资源管理器流量管理器配置文件，并且想要将两个应用添加为终结点。 要执行此操作，首先需要组合现有流量管理器配置文件 ID。 需要订阅 ID、资源组名称和现有流量管理器配置文件名称。

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

获取流量管理器 ID 之后，根据以下命令演示在新应用中创建源应用的克隆，同时将它们添加到现有流量管理器配置文件：

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>当前限制
下面是应用克隆的已知限制：

* 不会克隆自动缩放设置
* 不会克隆备份计划设置
* 不会克隆 VNET 设置
* 不会自动在目标应用上设置 App Insights
* 不会克隆简易身份验证设置
* 不会克隆 Kudu 扩展
* 不会克隆 TiP 规则
* 不会克隆数据库内容
* 如果克隆到不同的缩放单元，出站 IP 地址会更改
* 不适用于 Linux 应用

### <a name="references"></a>参考
* [应用服务克隆](app-service-web-app-cloning.md)
* [在 Azure 应用服务中备份应用](manage-backup.md)
* [Azure 流量管理器预览版对 Azure 资源管理器的支持](../traffic-manager/traffic-manager-powershell-arm.md)
* [应用服务环境简介](environment/intro.md)
* [将 Azure PowerShell 与 Azure 资源管理器配合使用](../azure-resource-manager/manage-resources-powershell.md)

