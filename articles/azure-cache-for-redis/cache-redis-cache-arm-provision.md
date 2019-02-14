---
title: 使用 Azure 资源管理器预配 Azure Redis 缓存 | Microsoft Docs
description: 使用 Azure 资源管理器模板部署 Azure Redis 缓存。
services: app-service
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: e223cb060857d45d9f25e2ee1dfca7e159225d8b
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237104"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>使用模板创建 Azure Redis 缓存

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本主题将介绍如何创建 Azure 资源管理器模板，以便部署 Azure Redis 缓存。 该缓存可以用于现有存储帐户以保存诊断数据。 还将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。 可将此模板用于自己的部署，或自定义此模板以满足要求。

目前，对订阅的同一区域中的所有缓存共享诊断设置。 更新区域中的一个缓存会影响该区域中的所有其他缓存。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](../azure-resource-manager/resource-group-authoring-templates.md)。 若要了解缓存资源类型的 JSON 语法和属性，请参阅 [Microsoft.Cache 资源类型](/azure/templates/microsoft.cache/allversions)。

有关完整的模板，请参阅 [Azure Redis 缓存模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)。

> [!NOTE]
> 适用于新[高级层](cache-premium-tier-intro.md)的 Resource Manager 模板现已推出。 
> 
> * [通过群集创建高级 Azure Redis 缓存](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [通过数据暂留创建高级 Azure Redis 缓存](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [通过 VNet 和可选群集创建高级 Azure Redis 缓存](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> 若要检查最新模板，请参阅 [Azure 快速入门模板](https://azure.microsoft.com/documentation/templates/)并搜索 `Azure Cache for Redis`。
> 
> 

## <a name="what-you-will-deploy"></a>将部署的内容
在此模板中，将部署 Azure Redis 缓存，以便使用现有存储帐户保存诊断数据。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。 该模板具有一个名为 Parameters 的部分，其中包含所有参数值。
应该为随着要部署的项目或要部署到的环境而变化的值定义参数。 不要为始终保持不变的值定义参数。 每个参数值可在模板中用来定义所部署的资源。 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Azure Redis 缓存的位置。 为获得最佳性能，请使用要与缓存配合使用的应用所在的同一位置。

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
要用于诊断的现有存储帐户的名称。 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
一个布尔值，该值指示是否允许通过非 SSL 端口访问。

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
一个值，该值指示是否启用诊断。 使用 ON 或 OFF。

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>要部署的资源
### <a name="azure-cache-for-redis"></a>用于 Redis 的 Azure 缓存
创建 Azure Redis 缓存。

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


