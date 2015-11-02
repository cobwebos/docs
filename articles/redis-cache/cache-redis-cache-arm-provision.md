<properties 
	pageTitle="预配 Redis 缓存" 
	description="使用 Azure 资源管理器模板部署 Azure Redis 缓存。" 
	services="redis-cache" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.date="09/15/2015" 
	wacn.date=""/>

# 使用模板创建 Redis 缓存

在本主题中，你将学习如何创建用于部署 Azure Redis 缓存的 Azure 资源管理器模板。该缓存可以用于现有存储帐户以保存诊断数据。你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。可将此模板用于自己的部署，或自定义此模板以满足要求。

目前，对订阅的同一区域中的所有缓存共享诊断设置。更新区域中的一个缓存将会影响该区域中的所有其他缓存。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](/documentation/articles/resource-group-authoring-templates)。

有关完整的模板，请参阅 [Redis 缓存模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)。

## 将部署的内容

在此模板中，你将部署 Azure Redis 缓存，以便使用现有存储帐户保存诊断数据。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://manage.windowsazure.cn#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Parameters

使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。该模板具有一个名为 Parameters 的部分，其中包含所有参数值。
你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。不要为永远保持不变的值定义参数。每个参数值可在模板中用来定义所部署的资源。

下面介绍模板中的每个参数。

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Redics 缓存的位置。为获得最佳性能，请使用要与缓存配合使用的应用所在的同一位置。

    "redisCacheLocation": {
      "type": "string"
    }

### diagnosticsStorageAccountName

要用于诊断的现有存储帐户的名称。

    "diagnosticsStorageAccountName": {
      "type": "string"
    }

### enableNonSslPort

一个布尔值，该值指示是否允许通过非 SSL 端口访问。

    "enableNonSslPort": {
      "type": "bool"
    }

### diagnosticsStatus

一个值，该值指示是否启用诊断。使用 ON 或 OFF。

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## 要部署的资源

### Redis Cache

创建 Azure Redis 缓存

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "redisVersion": "[parameters('redisCacheVersion')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
        "resources": [
          {
            "apiVersion": "2014-04-01",
            "type": "diagnosticSettings",
            "name": "service", 
            "location": "[parameters('redisCacheLocation')]",
            "dependsOn": [
              "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
            ],
            "properties": {
              "status": "[parameters('diagnosticsStatus')]",
              "storageAccountName": "[parameters('diagnosticsStorageAccountName')]",
              "retention": "30"
            }
          }
        ]
    }

## 运行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=76-->