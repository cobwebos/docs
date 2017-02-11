---
title: "在 Azure App Service 上进行高密度托管 | Microsoft Docs"
description: "在 Azure App Service 上进行高密度托管"
author: btardif
manager: wpickett
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/24/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7a4105feb1621891e1777078c67e080c44e7be51


---
# <a name="high-density-hosting-on-azure-app-service"></a>在 Azure App Service 上进行高密度托管
使用应用服务时，应用程序会与所分配的容量分离，具体表现在两个概念：

* **应用程序：**代表应用及其运行时配置。 例如，它包括运行时应该加载的 .NET 的版本、应用设置等。
* **应用服务计划：**定义容量的特征、可用功能集以及应用程序的位置。 例如，特征可能包括：大型（四核）机、四个实例、美国东部的高级功能。

一个应用始终与一个应用服务计划相关联，但一个应用服务计划则可能为一个或多个应用提供容量。

因此，平台既可以让单个应用处于隔离状态，也可以让多个应用通过共享应用服务计划来共享资源，具有相当的灵活性。

但是，当多个应用共享一个应用服务计划时，该应用的实例会在该应用服务计划的每个实例上运行。

## <a name="per-app-scaling"></a>按应用缩放
*按应用调整规模*是一项在应用服务计划级别启用的功能，可用于每个应用程序。

按应用缩放在缩放应用时独立于所属的应用服务计划。 可通过这种方式将应用服务计划配置为提供 10 个实例，而应用则可设置为仅缩放成其中 5 个的规模。

以下 Azure Resource Manager 模板所创建的应用服务计划扩展成 10 个实例，而所创建的应用配置为使用每个应用缩放，仅缩放成 5 个实例。

应用服务计划将**每个站点缩放**属性设置为 true ( `"perSiteScaling": true`)。 应用将要使用的**辅助角色数量** 设置为 5 (`"properties": { "numberOfWorkers": "5" }`)。

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
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
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


## <a name="recommended-configuration-for-high-density-hosting"></a>高密度托管的建议配置
按应用缩放是一项功能，在公共的 Azure 区域和应用服务环境中均可启用。 但是，根据建议的策略，应通过应用服务环境充分利用其高级功能以及更大型的容量池。  

按以下步骤为应用配置高密度托管：

1. 配置应用服务环境，选择专用于高密度托管方案的辅助池。
2. 创建单个应用服务计划，通过缩放即可使用辅助池的所有可用容量。
3. 在应用服务计划中将按站点缩放标志设置为 true。
4. 创建了新站点，并将该站点分配给 **numberOfWorkers** 属性设置为 **1** 的应用服务计划。 使用此配置，让该辅助池中产生可能的最高密度。
5. 可以按站点独立配置辅助角色数，根据需要授予其他资源。 例如，使用率高的站点可通过将 **numberOfWorkers** 设置为 **3** 来提高应用的处理能力，使用率低的站点则可将 **numberOfWorkers** 设置为 **1**。




<!--HONumber=Nov16_HO3-->


