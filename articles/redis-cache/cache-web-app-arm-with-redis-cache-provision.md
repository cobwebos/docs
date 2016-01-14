<properties 
	pageTitle="使用 Redis 缓存预配网站" 
	description="使用 Azure 资源管理器模板部署包含 Redis 缓存的网站。" 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags
	ms.service="app-service"
	ms.date="12/16/2015"
	wacn.date=""/>

# 使用模板创建网站和 Redis 缓存

在本主题中，你将学习如何创建用于部署 Azure 网站和 Redis 缓存的 Azure 资源管理器模板。你将了解如何定义要部署的资源以及如何定义执行部署时指定的参数。可将此模板用于自己的部署，或自定义此模板以满足要求。

有关创建模板的详细信息，请参阅[创作 Azure 资源管理器模板](/documentation/articles/resource-group-authoring-templates)。

有关完整的模板，请参阅[包含 Redis 缓存的网站模板](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)。

## 将部署的内容

在此模板中，你将部署：

- Azure 网站
- Azure Redis 缓存。


## 要指定的参数

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../includes/app-service-web-deploy-web-parameters.md)]

[AZURE.INCLUDE [cache-deploy-parameters](../includes/cache-deploy-parameters.md)]



## 要部署的资源

[AZURE.INCLUDE [app-service-web-deploy-web-host](../includes/app-service-web-deploy-web-host.md)]

### Redis Cache

创建用于网站的 Azure Redis 缓存。缓存的名称在 **redisCacheName** 参数中指定。

模板将在网站所在的同一位置创建缓存，为获得最佳性能，建议这样做。

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('siteLocation')]",
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

### 网站

使用 **siteName** 参数中指定的名称创建网站。

请注意，在网站中配置的应用设置属性使其可与 Redis 缓存配合工作。此应用设置是根据部署期间提供了值动态创建的。
        
    {
      "apiVersion": "2015-04-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[parameters('siteLocation')]",
      "dependsOn": [
          "[resourceId('Microsoft.Web/serverFarms', parameters('hostingPlanName'))]",
          "[resourceId('Microsoft.Cache/Redis', parameters('redisCacheName'))]"
      ],
      "properties": {
          "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
          {
              "apiVersion": "2015-06-01",
              "type": "config",
              "name": "web",
              "dependsOn": [
                  "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
              ],
              "properties": {
                  "appSettings": [
                      {
                          "name": "REDIS_HOST",
                          "value": "[concat(parameters('siteName'), '.redis.cache.chinacloudapi.cn:6379')]"
                      },
                      {
                          "name": "REDIS_KEY",
                          "value": "[listKeys(resourceId('Microsoft.Cache/Redis', parameters('redisCacheName')), '2014-04-01').primaryKey]"
                      }
                  ]
              }
          }
      ]
    }



## 运行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=Mooncake_0104_2016-->