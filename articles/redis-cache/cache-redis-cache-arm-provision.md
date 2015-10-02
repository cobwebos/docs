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
	ms.date="06/29/2015" 
	wacn.date=""/>

# 使用模板创建 Redis 缓存

在本主题中，你将学习如何创建用于部署 Azure Redis 缓存的 Azure 资源管理器模板。你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。可将此模板用于自己的部署，或自定义此模板以满足要求。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](../resource-group-authoring-templates.md)。

有关完整的模板，请参阅 [Redis 缓存模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)。

## 将部署的内容

在此模板中，你将部署 Azure Redis 缓存。

若要自动运行部署，请单击以下按钮：

[![部署到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## Parameters

使用 Azure 资源管理器，可以定义在部署模板时想要指定的值的参数。该模板具有一个名为 Parameters 的部分，其中包含所有参数值。你应该为随着要部署的项目或要部署到的环境而变化的值定义参数。不要为永远保持不变的值定义参数。每个参数值可在模板中用来定义所部署的资源。

下面介绍模板中的每个参数。

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Redics 缓存的位置。为获得最佳性能，请使用要与缓存配合使用的应用所在的同一位置。

    "redisCacheLocation": {
      "type": "string"
    }

    
## 要部署的资源

### Redis Cache

创建 Azure Redis 缓存

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }
     



## 运行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=71-->