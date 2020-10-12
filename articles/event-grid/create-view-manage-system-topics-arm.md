---
title: 使用 Azure 资源管理器模板在 Azure 事件网格中创建系统主题
description: 本文说明如何使用 Azure 资源管理器模板在 Azure 事件网格中创建系统主题。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1c8881a2d9dfca43084cc537b106e84b050a18d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115156"
---
# <a name="create-system-topics-in-azure-event-grid-using-resource-manager-templates"></a>使用资源管理器模板在 Azure 事件网格中创建系统主题
本文说明如何使用资源管理器模板创建和管理系统主题。 有关系统主题的概述，请参阅[系统主题](system-topics.md)。

## <a name="create-system-topic-first-and-then-create-event-subscription"></a>首先创建系统主题，然后创建事件订阅
若要先在 Azure 源上创建系统主题，然后再为该主题创建事件订阅，可以使用类似于下面的模板： 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Provide a unique name for the Blob Storage account."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Provide a location for the Blob Storage account that supports Event Grid."
            }
        },
        "eventSubName": {
            "type": "string",
            "defaultValue": "subToStorage",
            "metadata": {
                "description": "Provide a name for the Event Grid subscription."
            }
        },
        "endpoint": {
            "type": "string",
            "metadata": {
                "description": "Provide the URL for the WebHook to receive events. Create your own endpoint for events."
            }
        },
        "systemTopicName": {
            "type": "String",
            "defaultValue": "mystoragesystemtopic",
            "metadata": {
                "description": "Provide a name for the system topic."
            }
        }
    },
    "resources": [
        {
            "name": "[parameters('storageName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-10-01",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "accessTier": "Hot"
            }
        },
        {
            "name": "[parameters('systemTopicName')]",
            "type": "Microsoft.EventGrid/systemTopics",
            "apiVersion": "2020-04-01-preview",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[parameters('storageName')]"
            ],
            "properties": {
                "source": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageName'))]",
                "topicType": "Microsoft.Storage.StorageAccounts"
            }
        },
        {
            "type": "Microsoft.EventGrid/systemTopics/eventSubscriptions",
            "apiVersion": "2020-04-01-preview",
            "name": "[concat(parameters('systemTopicName'), '/', parameters('eventSubName'))]",
            "dependsOn": [
                "[resourceId('Microsoft.EventGrid/systemTopics', parameters('systemTopicName'))]"
            ],
            "properties": {
                "destination": {
                    "properties": {
                        "endpointUrl": "[parameters('endpoint')]"
                    },
                    "endpointType": "WebHook"
                },
                "filter": {
                    "includedEventTypes": [
                        "Microsoft.Storage.BlobCreated",
                        "Microsoft.Storage.BlobDeleted"
                    ]
                }
            }
        }
    ]
}
```

参阅[使用 Azure 资源管理器模板将 Blob 存储事件路由到 Web 终结点](blob-event-quickstart-template.md)，以获取有关使用资源管理器模板为它们创建系统主题和订阅的说明。 

## <a name="create-system-topic-while-creating-an-event-subscription"></a>创建事件订阅时创建系统主题 
若要在 Azure 源上创建事件订阅的同时隐式创建系统主题，可以使用以下模板：

``` json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string",
            "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
            "metadata": {
                "description": "Provide a unique name for the Blob Storage account."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Provide a location for the Blob Storage account that supports Event Grid."
            }
        },
        "eventSubName": {
            "type": "string",
            "defaultValue": "subToStorage",
            "metadata": {
                "description": "Provide a name for the Event Grid subscription."
            }
        },
        "endpoint": {
            "type": "string",
            "metadata": {
                "description": "Provide the URL for the WebHook to receive events. Create your own endpoint for events."
            }
        }
    },
    "resources": [
        {
            "name": "[parameters('storageName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2017-10-01",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "accessTier": "Hot"
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
            "apiVersion": "2018-01-01",
            "dependsOn": [
                "[parameters('storageName')]"
            ],
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "[parameters('endpoint')]"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": [
                        "All"
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤
请参阅 [Azure 事件网格中的系统主题](system-topics.md)部分，详细了解 Azure 事件网格支持的系统主题和主题类型。 
