---
title: 使用按应用缩放的高密度托管-Azure App Service |Microsoft Docs
description: 在 Azure 应用服务上进行高密度托管
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 7130c9547e0778ce40a0ad1c1ea41607a02df23e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088105"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>使用按应用缩放的 Azure App Service 上的高密度托管

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

使用应用服务时, 可以通过缩放应用运行的[应用服务计划](overview-hosting-plans.md)来缩放应用。 当多个应用在同一个应用服务计划中运行时，每个横向扩展实例会在计划中运行所有应用。

可在应用服务计划级别启用*按应用缩放*, 以允许独立于托管应用的应用服务计划缩放应用。 这样，可以将一个应用服务计划扩展到 10 个实例，而将一个应用设置为仅用 5 个。

> [!NOTE]
> 按应用缩放仅适用于标准、高级、高级 V2 和独立定价层。
>

将应用分配给可用的应用服务计划, 使用最大努力方式实现跨实例的分布。 尽管不能保证均匀分布, 但平台将确保同一应用的两个实例不会托管在同一个应用服务计划实例上。

平台不依赖于指标来决定辅助角色分配。 仅当在应用服务计划中添加或删除实例时, 应用程序才重新平衡。

## <a name="per-app-scaling-using-powershell"></a>使用 PowerShell 的按应用缩放

通过将 ```-PerSiteScaling $true``` 参数传入 ```New-AzAppServicePlan``` cmdlet，创建按应用缩放的计划。

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

通过将 `-PerSiteScaling $true` 参数传入 ```Set-AzAppServicePlan``` cmdlet，使用现有应用服务计划启用按应用缩放。

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

在应用级别，配置应用可以在应用服务计划中使用的实例数。

在以下示例中，无论基础应用服务计划扩展到多少个实例，应用都被限制为两个实例。

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` 不同于 `$newapp.MaxNumberOfWorkers`。 按应用缩放使用 `$newapp.SiteConfig.NumberOfWorkers` 来确定应用的缩放特征。

## <a name="per-app-scaling-using-azure-resource-manager"></a>使用 Azure 资源管理器的按应用缩放

以下 Azure 资源管理器模板创建：

- 扩展到 10 个实例的应用服务计划
- 配置为最多扩展到 5 个实例的应用。

应用服务计划将 **PerSiteScaling** 属性设置为 true `"perSiteScaling": true`。 应用将要使用的**辅助角色数量** 设置为 5 `"properties": { "numberOfWorkers": "5" }`。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

按应用缩放是一项功能，在全球 Azure 区域和[应用服务环境](environment/app-service-app-service-environment-intro.md)中均可启用。 不过, 推荐的策略是使用应用服务环境来利用其高级功能和更大的应用服务计划容量。  

按以下步骤为应用配置高密度托管：

1. 将应用服务计划指定为高密度计划, 并将其扩展到所需的容量。
1. 在应用服务计划中将 `PerSiteScaling` 标志设置为 true。
1. 将 **numberOfWorkers** 属性设置为 **1**，创建新应用并将其分配给该应用服务计划。
   - 使用此配置可以产生最大的密度。
1. 可按应用独立配置辅助角色数，根据需要授予其他资源。 例如：
   - 使用率高的应用可通过将 **numberOfWorkers** 设置为  **3** 来提高该应用的处理能力。
   - 使用率低的应用可将 **numberOfWorkers** 属性设置为 **1**。

## <a name="next-steps"></a>后续步骤

- [Azure 应用服务计划深入概述](overview-hosting-plans.md)
- [应用服务环境简介](environment/app-service-app-service-environment-intro.md)
