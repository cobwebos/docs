### App Service 计划

创建用于托管网站的服务计划。通过 **hostingPlanName** 参数提供计划的名称。计划的位置与用于网站的位置相同。定价层和辅助角色大小在 **sku** 和 **workerSize** 参数中指定

    {
      "apiVersion": "2014-06-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[parameters('siteLocation')]",
      "properties": {
        "name": "[parameters('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "numberOfWorkers": 1
      }
    },

<!---HONumber=Mooncake_0118_2016-->