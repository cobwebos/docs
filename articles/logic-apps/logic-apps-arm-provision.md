---
title: "在 Azure 中使用模板创建逻辑应用 | Microsoft 文档"
description: "使用 Azure Resource Manager 模板部署用于定义工作流的逻辑应用。"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 1305fdc4cf87905eef701ec5471d6329006c2b5f
ms.contentlocale: zh-cn
ms.lasthandoff: 01/31/2017


---
# <a name="create-a-logic-app-using-a-template"></a>使用模板创建逻辑应用
若要在逻辑应用中使用不同的连接器，可以使用模板这种快速方法。 逻辑应用包括 Azure Resource Manager 模板，方便用户创建可以用来定义业务工作流的逻辑应用。 可以定义要部署的资源，以及定义在部署逻辑应用时如何定义参数。 可将此模板用于自己的业务方案，或根据需要自定义此模板。

有关逻辑应用属性的详细信息，请参阅[逻辑应用工作流管理 API](https://msdn.microsoft.com/library/azure/mt643788.aspx)。 

有关定义本身的示例，请参阅[创作逻辑应用定义](logic-apps-author-definitions.md)。 

有关创建模板的详细信息，请参阅[创作 Azure Resource Manager 模板](../azure-resource-manager/resource-group-authoring-templates.md)。

有关完整的模板，请参阅[逻辑应用模板](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)。

## <a name="what-you-deploy"></a>部署内容
使用此模板可部署逻辑应用。

若要自动运行部署，请选择以下按钮：  

[![部署到 Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>parameters
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>要部署的资源
### <a name="logic-app"></a>逻辑应用
创建逻辑应用。

模板使用一个参数值作为逻辑应用的名称。 将逻辑应用的位置设置为与资源组相同的位置。 

此特定定义每小时运行一次，并对 **testUri** 参数中指定的位置执行 ping 命令。 

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
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
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


## <a name="commands-to-run-deployment"></a>运行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup




