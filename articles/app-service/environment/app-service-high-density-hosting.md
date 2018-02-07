---
title: "在 Azure 应用服务上进行高密度托管 | Microsoft Docs"
description: "在 Azure 应用服务上进行高密度托管"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 22/01/2018
ms.author: byvinyal
ms.openlocfilehash: 2ffffd3cc9f5c59f74f71d6d7d31c5ea615d11f4
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="high-density-hosting-on-azure-app-service"></a>在 Azure 应用服务上进行高密度托管
使用应用服务时，应用程序会与所分配的容量分离，具体表现在两个概念：

* **应用程序：**代表应用及其运行时配置。 例如，它包括运行时应该加载的 .NET 的版本、应用设置。
* **应用服务计划：**定义容量的特征、可用功能集以及应用程序的位置。 例如，特征可能包括：大型（四核）机、四个实例、美国东部的高级功能。

一个应用始终与一个应用服务计划相关联，但一个应用服务计划则可能为一个或多个应用提供容量。

因此，平台既可以让单个应用处于隔离状态，也可以让多个应用通过共享应用服务计划来共享资源，具有相当的灵活性。

但是，当多个应用共享一个应用服务计划时，该应用的实例会在该应用服务计划的每个实例上运行。

## <a name="per-app-scaling"></a>按应用缩放
*按应用调整规模*是一项在应用服务计划级别启用的功能，可用于每个应用程序。

按应用缩放在缩放应用时独立于所属的应用服务计划。 这样，可以将一个应用服务计划扩展到 10 个实例，而将一个应用设置为仅用 5 个。

   >[!NOTE]
   >按应用缩放仅适用于**标准**、**高级**、**高级 V2** 和**独立** SKU 应用服务计划
   >

### <a name="per-app-scaling-using-powershell"></a>使用 PowerShell 的按应用缩放

通过将 ```-perSiteScaling $true``` 属性传入 ```New-AzureRmAppServicePlan``` commandlet，创建配置为“按应用缩放”计划的计划

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

如果想要更新现有应用服务计划以使用此功能，可： 

- 获取目标计划 ```Get-AzureRmAppServicePlan```
- 本地修改属性```$newASP.PerSiteScaling = $true```
- 将更改发布回 Azure ```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

在应用级别，配置应用可以在应用服务计划中使用的实例数。

在以下示例中，无论基础应用服务计划扩展到多少个实例，应用都被限制为两个实例。

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp.SiteConfig.NumberOfWorkers 不同于 $newapp.MaxNumberOfWorkers。 按应用缩放使用 $newapp.SiteConfig.NumberOfWorkers 来确定应用的缩放特征。

### <a name="per-app-scaling-using-azure-resource-manager"></a>使用 Azure 资源管理器的按应用缩放

以下 Azure 资源管理器模板创建：

- 扩展到 10 个实例的应用服务计划
- 配置为最多扩展到 5 个实例的应用。

应用服务计划将 **PerSiteScaling** 属性设置为 true ```"perSiteScaling": true```。 应用将要使用的**辅助角色数量** 设置为 5 ```"properties": { "numberOfWorkers": "5" }```。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>高密度托管的建议配置
按应用缩放是一项功能，在全球 Azure 区域和应用服务环境中均可启用。 但是，根据建议的策略，应通过应用服务环境充分利用其高级功能以及更大型的容量池。  

按以下步骤为应用配置高密度托管：

1. 配置应用服务环境，选择专用于高密度托管方案的辅助池。
1. 创建单个应用服务计划，通过缩放即可使用辅助池的所有可用容量。
1. 在应用服务计划中将 PerSiteScaling 标志设置为 true。
1. 将 **numberOfWorkers** 属性设置为 **1**，创建新应用并将其分配给该应用服务计划。 使用此配置，让该辅助池中产生可能的最高密度。
1. 可按应用独立配置辅助角色数，根据需要授予其他资源。 例如：
    - 使用率高的应用可通过将 **numberOfWorkers** 设置为  **3** 来提高该应用的处理能力。 
    - 使用率低的应用可将 **numberOfWorkers** 属性设置为 **1**。

## <a name="next-steps"></a>后续步骤

- [Azure 应用服务计划深入概述](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [应用服务环境简介](app-service-app-service-environment-intro.md)
