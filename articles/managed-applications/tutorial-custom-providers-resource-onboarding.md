---
title: 使用 Azure 自定义提供程序进行资源载入
description: 通过使用自定义提供程序进行资源载入，你可以操作和扩展现有的 Azure 资源。
services: managed-applications
ms.service: managed-applications
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 45086987d5ba3a619028ced798712f2870c6d487
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826788"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>教程：使用 Azure 自定义提供程序进行资源载入

在本教程中，将部署到 Azure 自定义资源提供程序，该提供程序使用 Microsoft.CustomProviders/associations 资源类型扩展 Azure 资源管理器 API。 本教程介绍如何扩展自定义提供程序实例所在的资源组之外的现有资源。 在本教程中，自定义资源提供程序由 Azure 逻辑应用提供支持，但你可以使用任何公共 API 终结点。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要知道：

* [Azure 自定义提供程序](custom-providers-overview.md)的功能。
* 有关[使用自定义提供程序进行资源载入](concepts-custom-providers-resourceonboarding.md)的基本信息。

## <a name="get-started-with-resource-onboarding"></a>资源载入入门

在本教程中，需要部署两个部分：自定义提供程序和关联。 若要使此过程更加轻松，可以选择使用单一模板来部署这两种方法。

该模板将使用以下资源：

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>部署自定义提供程序基础结构

模板的第一部分部署自定义提供程序基础结构。 此基础结构定义关联资源的影响。 如果你不熟悉自定义提供程序，请参阅[自定义提供程序基础知识](custom-providers-overview.md)。

让我们来部署自定义提供程序基础结构。 复制、保存并部署上述模板，或使用 Azure 门户，然后按照说明操作并部署基础结构。

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 在“所有服务”  中  或使用主搜索框搜索“模板”：

   ![搜索模板](media/custom-providers-resource-onboarding/templates.png)

3. 在“模板”窗格中，选择“添加”   ：

   ![选择“添加”](media/custom-providers-resource-onboarding/templatesadd.png)

4. 在“常规”下，输入新模板的“名称”和“说明”：   

   ![模板名称和说明](media/custom-providers-resource-onboarding/templatesdescription.png)

5. 通过在本文的“资源载入入门”部分中复制 JSON 模板来创建资源管理器模板：

   ![创建资源管理器模板](media/custom-providers-resource-onboarding/templatesarmtemplate.png)

6. 选择“添加”  以创建模板。 如果未显示新模板，请选择“刷新”  。

7. 选择新创建的模板，然后选择“部署”  ：

   ![选择新模板，然后选择“部署”](media/custom-providers-resource-onboarding/templateselectspecific.png)

8. 输入必填字段的设置，然后选择订阅和资源组。 可以将“自定义资源提供程序 ID”  框留空。

   | 设置名称 | 必需？ | 说明 |
   | ------------ | -------- | ----------- |
   | 位置 | 是 | 模板中资源的位置。 |
   | 逻辑应用名称 | 否 | 逻辑应用的名称。 |
   | 自定义资源提供程序名称 | 否 | 自定义资源提供程序的名称。 |
   | 自定义资源提供程序 ID | 否 | 支持关联资源的现有自定义资源提供程序。 如果在此处指定一个值，则将跳过逻辑应用和自定义提供程序部署。 |
   | 关联名称 | 否 | 关联资源的名称。 |

   示例参数：

   ![输入模板参数](media/custom-providers-resource-onboarding/templatescustomprovider.png)

9. 转到部署，等待部署完成。 应当会看到类似以下屏幕截图的内容。 应当会看到新的关联资源作为输出：

   ![成功部署](media/custom-providers-resource-onboarding/customproviderdeployment.png)

   下面是资源组，其中选择了“显示隐藏的类型”  ：

   ![自定义提供程序部署](media/custom-providers-resource-onboarding/showhidden.png)

10. 浏览逻辑应用的“运行历史记录”  选项卡，查看对关联创建的调用：

    ![逻辑应用运行历史记录](media/custom-providers-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>部署其他关联

设置自定义提供程序基础结构后，可以轻松地部署更多关联。 其他关联的资源组不一定要与部署自定义提供程序基础结构的资源组相同。 若要创建关联，需要对指定的自定义资源提供程序 ID 具有 Microsoft.CustomProviders/resourceproviders/write 权限。

1. 转到上一个部署的资源组中的自定义提供程序“Microsoft.CustomProviders/resourceProviders”  资源。 需要选中“显示隐藏的类型”  复选框：

   ![转到资源](media/custom-providers-resource-onboarding/showhidden.png)

2. 复制自定义提供程序的资源 ID 属性。

3. 在“所有服务”  中  或使用主搜索框搜索“模板”：

   ![搜索模板](media/custom-providers-resource-onboarding/templates.png)

4. 选择以前创建的模板，然后选择“部署”  ：

   ![选择以前创建的模板，然后选择“部署”](media/custom-providers-resource-onboarding/templateselectspecific.png)

5. 输入必填字段的设置，然后选择订阅和其他资源组。 对于“自定义资源提供程序 ID”  设置，输入从之前部署的自定义提供程序复制的资源 ID。

6. 转到部署，等待部署完成。 它现在应仅部署新的关联资源：

   ![新关联资源](media/custom-providers-resource-onboarding/createdassociationresource.png)

如果需要，可以返回到逻辑应用的“运行历史记录”  ，查看是否对逻辑应用进行了另一次调用。 可以更新逻辑应用，为每个已创建的关联增加额外的功能。

## <a name="getting-help"></a>获取帮助

如果你遇到了 Azure 自定义提供程序方面的问题，请尝试在 [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-custom-providers) 上提问。 该论坛上可能已有类似问题的解答，因此，在发贴之前请先查看以往的提问。 添加标记 `azure-custom-providers` 可以快速得到回复！

