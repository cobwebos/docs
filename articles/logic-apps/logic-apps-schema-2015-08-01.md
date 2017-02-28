---
title: "新的 2015-08-01-预览架构版本"
description: "了解如何为最新版本的逻辑应用编写 JSON 定义"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2016
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: 061b68ec5fc9c14fd0c3f00cc0be9f09717fc1ad
ms.openlocfilehash: 8f1e2680aff5c1d5c9b90654a334cae2245db5fb
ms.lasthandoff: 02/15/2017


---
# <a name="new-schema-version-2015-08-01-preview"></a>新的 2015-08-01-预览架构版本
Azure 逻辑应用的新架构和 API 版本具有大量改进，提高了逻辑应用的可靠性和易用性。 有四个重大改变：

1. **APIApp** 操作类型已更新为新的 **APIConnection** 操作类型。
2. **Repeat** 已重命名为 **Foreach**。
3. 不再需要 **HTTP 侦听器** API 应用。
4. 调用子工作流会使用新架构。

## <a name="1-moving-to-api-connections"></a>1.移动到 API 连接
最大的变化是不再需要将 API 应用部署到 Azure 订阅即可使用 API。 可以通过 2 种方法来使用 API：

* 托管 API
* 自定义 Web API

每种方法的处理方式略有不同，因为其管理和托管模型不同。 此模型的一个优点是不再局限于在资源组中部署的资源。 

### <a name="managed-apis"></a>托管 API
Microsoft 代表你管理某些 API，例如 Office 365、Salesforce、Twitter 和 FTP。 某些托管 API 可以按原样使用（例如必应翻译），另一些 API 需要进行配置。 此配置称为“连接”。

例如，在使用 Office 365 时，需要创建包含 Office 365 登录令牌的连接。 此令牌以安全方式存储和刷新，以便逻辑应用可以始终调用 Office 365 API。 或者，如果要连接到 SQL 或 FTP 服务器，则需要创建具有连接字符串的连接。 

在此定义中，这些操作名为 `APIConnection`。 下面是调用 Office 365 以发送电子邮件的连接的示例：

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

`host` 对象是特定于 API 连接的输入的一部分，并包含两个部分：`api` 和 `connection`。

`api` 具有承载托管 API 的位置的运行时 URL。 可以通过调用 `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview` 来查看所有可用的托管 API。

使用 API 时，API 可以定义也可以不定义任何**连接参数**。 如果 API 未定义连接参数，则不需要**连接**。 如果 API 定义了连接参数，则必须创建连接。 创建连接时，连接具有你选择的名称。 然后，你可以在 `host` 对象内的 `connection` 对象中引用该名称。 若要在资源组中创建连接，请调用：

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

使用以下正文：

```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
    "parameterValues": {
        "accountName": "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location": "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 模板中部署托管 API
只要不要求进行交互式登录，便可以在 Azure Resource Manager 模板中创建完整应用程序。
如果要求登录，则可以使用 Azure Resource Manager 模板执行所有设置，但还必须访问门户来对连接进行授权。 

```
    "resources": [{
        "apiVersion": "2015-08-01-preview",
        "name": "azureblob",
        "type": "Microsoft.Web/connections",
        "location": "[resourceGroup().location]",
        "properties": {
            "api": {
                "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
            },
            "parameterValues": {
                "accountName": "[parameters('storageAccountName')]",
                "accessKey": "[parameters('storageAccountKey')]"
            }
        }
    }, {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-08-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"
        ],
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
                "actions": {
                    "Create_file": {
                        "type": "apiconnection",
                        "inputs": {
                            "host": {
                                "api": {
                                    "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                                },
                                "connection": {
                                    "name": "@parameters('$connections')['azureblob']['connectionId']"
                                }
                            },
                            "method": "post",
                            "queries": {
                                "folderPath": "[concat('/', parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:, Hello+world!')",
                            "path": "/datasets/default/files"
                        },
                        "conditions": []
                    }
                },
                "contentVersion": "1.0.0.0",
                "outputs": {},
                "parameters": {
                    "$connections": {
                        "defaultValue": {},
                        "type": "Object"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "Recurrence",
                        "recurrence": {
                            "frequency": "Day",
                            "interval": 1
                        }
                    }
                }
            },
            "parameters": {
                "$connections": {
                    "value": {
                        "azureblob": {
                            "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                            "connectionName": "azureblob",
                            "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                        }

                    }
                }
            }
        }
    }]
```

在此示例中可以看到，连接只是位于资源组中的资源。 它们引用你的订阅中可供你使用的托管 API。

### <a name="your-custom-web-apis"></a>自定义 Web API
如果你使用自己的 API（具体而言，不是 Microsoft 托管的 API），则应使用内置 **HTTP** 操作调用它们。 若要获得理想体验，应该为 API 公开一个 Swagger 终结点。 此终结点使逻辑应用设计器可以呈现 API 的输入和输出。 如果没有 Swagger，则设计器只能将输入和输出显示为不透明的 JSON 对象。

下面是演示新 `metadata.apiDefinitionUrl` 属性的示例：

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata": {
              "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method": "GET"
            }
        }
    }
}
```

如果将 Web API 托管在**应用服务**中，则 Web API 会自动显示在设计器中提供的操作列表中。 如果未自动显示，则必须直接粘贴在 URL 中。 Swagger 终结点必须未经身份验证才能在逻辑应用设计器中使用（不过你可以使用 Swagger 中支持的任何方法保护 API 本身）。

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>将已部署的 API 应用与 2015-08-01-预览结合使用
如果以前部署了 API 应用，则可以通过 **HTTP** 操作调用它。

例如，如果使用 Dropbox 列出文件，则 **2014-12-01-preview** 架构版本定义可能具有与下面类似的内容：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

可以构造与下面类似的等效 HTTP 操作（逻辑应用定义的参数部分保持不变）：

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata": {
              "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method": "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

逐个演练这些属性：

| 操作属性 | 说明 |
| --- | --- |
| `type` |是 `Http` 而不是 `APIapp` |
| `metadata.apiDefinitionUrl` |若要在逻辑应用设计器中使用此操作，请包括元数据终结点，该终结点是通过以下方式构造的：`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard`。 |
| `inputs.uri` |构造方式：`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |始终是 `POST` |
| `inputs.body` |等同于 api 应用参数 |
| `inputs.authentication` |等同于 api 应用身份验证 |

此方法也适用于所有 API 应用操作。 但是请记住，这些以前的 API 应用不再受支持，你应改用上述两个其他选项中的一个（托管 API 或托管自定义 Web API）。

## <a name="2-repeat-renamed-to-foreach"></a>2.Repeat 重命名为 Foreach

对于以前的架构版本，很多客户反馈说，**Repeat** 让人困惑，并且没有正确地表达 **Repeat** 实际上是一个 for-each 循环。 因此，我们已将 **Repeat** 重名为 **Foreach**。 例如，以前将编写为：

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

现在将编写为：

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

函数 `@repeatItem()` 以前用来引用要循环访问的当前项。 此函数现在简化为 `@item()`。 

### <a name="referencing-the-outputs-of-the-foreach"></a>引用 Foreach 的输出
为了进一步简化，**Foreach** 操作的输出没有包装在名为 **repeatItems** 的对象中。 虽然以上 repeat 的输出原来是：

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

但这些输出现在是：

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
        }
        "status": "Succeeded"
    }
]
```

引用这些输出时，若要访问操作的正文，必须执行：

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "repeat": "@outputs('pingBing').repeatItems",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@repeatItem().outputs.body"
            }
        }
    }
}
```

现在，可以改为执行：

```
{
    "actions": {
        "secondAction": {
            "type": "Http",
            "foreach": "@outputs('pingBing')",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com",
                "body": "@item().outputs.body"
            }
        }
    }
}
```

由于进行了这些更改，因此删除了函数 `@repeatItem()`、`@repeatBody()` 和 `@repeatOutputs()`。

## <a name="3-native-http-listener"></a>3.本机 HTTP 侦听器
HTTP 侦听器功能现在是内置的，因此不再需要部署 HTTP 侦听器 API 应用。 在此处了解[有关如何使逻辑应用终结点可调用的完整详细信息](../logic-apps/logic-apps-http-endpoint.md)。 

由于进行了这些更改，因此删除了函数 `@accessKeys()`，并将它替换为 `@listCallbackURL()` 函数以便用于获取终结点（如果需要）。 此外，现在必须在逻辑应用中至少定义一个触发器。 如果要对工作流执行 `/run` 操作，则必须具有 `manual`、`apiConnectionWebhook` 或 `httpWebhook` 触发器之一。

## <a name="4-calling-child-workflows"></a>4.调用子工作流
以前，调用子工作流需要转到该工作流，获取访问令牌，然后将令牌粘贴到你要调用该子级的逻辑应用定义中。 借助新架构版本，逻辑应用引擎会在运行时自动为子工作流生成 SAS，这意味着不必将任何密码粘贴到定义中。  下面是一个示例：

```
"mynestedwf": {
    "type": "workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName": "myendpointtrigger"
        },
        "queries": {
            "extrafield": "specialValue"
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "conditions": []
}
```

第二个改进是我们为子工作流提供了对传入请求的完全访问权限。 这意味着可以在查询部分和标头对象中传递参数，并且可以完全自定义整个正文。

最后，需要对子工作流进行更改。 之前你可以直接调用子工作流；而现在，必须在工作流中为要调用的父级定义触发器终结点。 通常，这意味着会添加类型为**手动**的触发器，随后在父定义中使用该触发器。 请注意，`host` 属性明确具有 `triggerName`，因为你必须始终指定所调用的触发器。

## <a name="other-changes"></a>其他更改
### <a name="new-queries-property"></a>新查询属性
所有操作类型现在都支持名为**查询**的新输入。 此输入可以是一个结构化对象，而不必手动组合字符串。

### <a name="parse-function-renamed"></a>parse() 函数已重命名
我们即将添加更多内容类型，因此已将 `parse()` 函数重命名为 `json()`。

## <a name="coming-soon-enterprise-integration-apis"></a>即将推出：企业集成 API
当前，我们尚未提供企业集成 API（例如 AS2）的托管版本。 这些 API 已涵盖在[路线图](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)中，即将推出。 同时，你可以通过 HTTP 操作使用现有的已部署 BizTalk API（如上面“使用已部署的 API 应用”所述）。


