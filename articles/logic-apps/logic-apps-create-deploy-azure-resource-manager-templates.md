---
title: 通过 Azure 资源管理器模板创建逻辑应用 | Microsoft Docs
description: 使用 Azure 资源管理器模板创建和部署逻辑应用工作流
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 69be968f34d756cc725f5353dcf074c01fad65ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板创建和部署逻辑应用

使用 Azure 逻辑应用提供的 Azure 资源管理器模板，不仅可以创建用于自动处理工作流的逻辑应用，还可以定义对部署使用的资源和参数。 可以将此模板用于自己的业务方案，也可以根据需要自定义此模板。 详细了解[逻辑应用的资源管理器模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)和 [Azure 资源管理器模板结构和语法](../azure-resource-manager/resource-group-authoring-templates.md)。

## <a name="define-the-logic-app"></a>定义逻辑应用

此示例逻辑应用定义每小时运行一次，并对 `testUri` 参数中指定的位置执行 ping 命令。
模板使用逻辑应用名称参数值 (```logicAppName```)，以及要对其执行 ping 命令以供测试 (```testUri```) 的位置参数值。 详细了解[如何在模板中定义这些参数](#define-parameters)。 模板还将逻辑应用的位置设置为 Azure 资源组所在的位置。 

``` json
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2016-06-01",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "tags": {
      "displayName": "LogicApp"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "testURI": {
               "type": "string",
               "defaultValue": "[parameters('testUri')]"
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Hour",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Http": {
              "type": "Http",
              "inputs": {
                  "method": "GET",
                  "uri": "@parameters('testUri')"
              },
              "runAfter": {}
           }
         },
         "outputs": {}
      },
      "parameters": {}
   }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>定义参数

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

下面介绍了模板中的参数：

| 参数 | 说明 | JSON 定义示例 | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | 定义模板创建的逻辑应用名称。 | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | 定义要对其执行 ping 命令以供测试的位置。 | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

详细了解[逻辑应用工作流定义和属性的 REST API](https://docs.microsoft.com/rest/api/logic/workflows)，以及[如何使用 JSON 生成逻辑应用定义](logic-apps-author-definitions.md)。

## <a name="deploy-logic-apps-automatically"></a>自动部署逻辑应用

若要创建逻辑应用并将它自动部署到 Azure，请选择“部署到 Azure”，如下所示：

[![部署到 Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

执行此操作可以登录 Azure 门户。在此门户中，可以提供逻辑应用的详细信息，并能对模板或参数进行任何更改。 例如，Azure 门户提示输入以下详细信息：

* Azure 订阅名称
* 要使用的资源组
* 逻辑应用位置
* 逻辑应用名称
* 测试 URI
* 接受指定的条款和条件

## <a name="deploy-logic-apps-with-commands"></a>使用命令部署逻辑应用

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)