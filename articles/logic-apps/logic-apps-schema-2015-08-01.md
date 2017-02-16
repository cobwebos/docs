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
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: a7e1bbb8d670b20a530ea488f44ff56dc721609b


---
# <a name="new-schema-version-2015-08-01-preview"></a>新的 2015-08-01-预览架构版本
逻辑应用的新架构和 API 版本具有大量改进，可提高逻辑应用的可靠性和易用性。 有 4 个重要差异：

1. **APIApp** 操作类型已更新为新的 **APIConnection** 操作类型。
2. **Repeat** 已重命名为 **Foreach**。
3. 不再需要 **HTTP 侦听器** API 应用。
4. 调用子工作流会使用新架构。

## <a name="1-moving-to-api-connections"></a>1.移动到 API 连接
最大的变化是不再需要将 API 应用部署到 Azure 订阅即可使用 API。 可以通过 2 种方法来使用 API：

* 托管 API
* 自定义 Web API

每种 API 的处理方式略有不同，因为其管理和托管模型不同。 此模型的一个优点是不再局限于在资源组中部署的资源。 

### <a name="managed-apis"></a>托管 API
Microsoft 代表你托管了一些 API，如 Office 365、Salesforce、Twitter、FTP 等... 这些托管 API 中的一些可以按原样使用（如必应翻译），而其他 API 需要进行配置。 此配置称为连接。

例如在使用 Office 365 时，需要创建包含 Office 365 登录令牌的连接。 此令牌会以安全方式进行存储和刷新，以便逻辑应用可以始终调用 Office 365 API。 或者，如果要连接到 SQL 或 FTP 服务器，则需要创建具有连接字符串的连接。 

在定义内部，这些操作称为 `APIConnection`。 下面是调用 Office 365 以发送电子邮件的连接的示例：

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

对 API 连接唯一的输入部分是 `host` 对象。 这包含两个部件：`api` 和 `connection`。

`api` 具有承载托管 API 的位置的运行时 URL。 可以通过调用 `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview` 来查看所有可用托管 API。

使用 API 时，它可能定义也可能未定义任何**连接参数**。 如果未定义，则无需**连接**。 如果进行了定义，则必须创建连接。 创建该连接时，它会具有你选择的名称，然后你可在 `host` 对象内部的 `connection` 对象中引用该名称。 若要在资源组中创建连接，请调用：

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
    "parameterValues" : {
        "accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
    }
  },
  "location" : "{Logic app's location}"
}
```

### <a name="deploying-managed-apis-in-an-azure-resource-manager-template"></a>在 Azure Resource Manager 模板中部署托管 API
只要不需要交互式登录，便可以在 ARM 模板中创建完整应用程序。 如果需要登录，则可以使用 ARM 模板完成所有设置，但是仍然必须访问门户以对连接进行授权。 

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
        "dependsOn": [
            "[resourceId('Microsoft.Web/connections', 'azureblob')]"
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
                                "folderPath": "[concat('/',parameters('containerName'))]",
                                "name": "helloworld.txt"
                            },
                            "body": "@decodeDataUri('data:,Hello+world!')",
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

可以在此示例中看到连接只是处于资源组中的普通资源。 它们引用可供你在订阅中使用的 managedAPIs。

### <a name="your-custom-web-apis"></a>自定义 Web API
如果使用自己的 API（具体而言，不是 Microsoft 托管的 API），则应使用内置 **HTTP** 操作调用它们。 若要获得理想体验，应该为 API 公开 swagger 终结点。 这使逻辑应用设计器可以呈现 API 的输入和输出。 如果不使用 swagger，则设计器只能将输入和输出显示为不透明的 JSON 对象。

下面是演示新 `metadata.apiDefinitionUrl` 属性的示例：

```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

如果在**应用服务**中承载 Web API，则它会自动显示在设计器中提供的操作列表中。 如果未自动显示，则必须直接粘贴在 URL 中。 swagger 终结点必须未经身份验证才能在逻辑应用设计器中使用（不过你可以使用 Swagger 中支持的任何方法保护 API 本身）。

### <a name="using-your-already-deployed-api-apps-with-2015-08-01-preview"></a>将已部署的 API 应用与 2015-08-01-预览结合使用
如果以前部署了 API 应用，则可以通过 **HTTP** 操作调用它。

例如，如果使用 Dropbox 列出文件，则可能会在 **2014-12-01-预览**架构版本定义中获得与下面类似的内容：

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
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
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
| `metadata.apiDefinitionUrl` |如果要在逻辑应用设计器中使用此操作，则需要包含元数据终结点。 这通过以下方法进行构造：`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` |这通过以下方法进行构造：`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` |始终是 `POST` |
| `inputs.body` |等同于 api 应用参数 |
| `inputs.authentication` |等同于 api 应用身份验证 |

此方法也适用于所有 API 应用操作。 但是请记住，这些以前的 API 应用不再受支持，你应转向上面两个其他选项中的一个（托管 API 或承载自定义 Web API）。

## <a name="2-repeat-renamed-to-foreach"></a>2.Repeat 重命名为 Foreach
对于以前的架构版本，我们收到了大量客户反馈，表示 **Repeat** 令人困惑，无法正确指出它实际是 for each 循环。 因为，我们已将它重命名为 **Foreach**。 例如：

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

现在编写为：

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

以前函数 `@repeatItem()` 用于引用要循环访问的当前项。 这已简化为只是 `@item()`。 

### <a name="referencing-the-outputs-of-the-foreach"></a>引用 Foreach 的输出
为了进一步简化，**Foreach** 操作的输出不会在名为 **repeatItems** 的对象中进行包装。 然而，这意味着上面 repeat 的输出是：

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

现在它是：

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
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

现在，可以改为执行：

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

由于进行了这些更改，因此删除了函数 `@repeatItem()`、`@repeatBody()` 和 `@repeatOutputs()`。

## <a name="3-native-http-listener"></a>3.本机 HTTP 侦听器
HTTP 侦听器功能现在是内置的，因此不再需要部署 HTTP 侦听器 API 应用。 在此处了解[有关如何使逻辑应用终结点可调用的完整详细信息](../logic-apps/logic-apps-http-endpoint.md)。 

由于进行了这些更改，因此删除了函数 `@accessKeys()`，并将它替换为 `@listCallbackURL()` 函数以便用于获取终结点（如果需要）。 此外，现在必须在逻辑应用中定义至少一个触发器。 如果要对工作流执行 `/run` 操作，则需要具有 `manual`、`apiConnectionWebhook` 或 `httpWebhook` 触发器之一。 

## <a name="4-calling-child-workflows"></a>4.调用子工作流
以前，调用子工作流需要转到该工作流，获取访问令牌，然后将令牌粘贴到你要调用该子级的逻辑应用定义中。 借助新架构版本，逻辑应用引擎会在运行时为子工作流自动生成 SAS，这意味着不必将任何密码粘贴到定义中。  下面是一个示例：

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

第二个改进是我们为子工作流提供了对传入请求的完全访问权限。 这意味着可以在查询部分和标头对象中传递参数，并且可以完全自定义整个正文。

最后，需要对子工作流进行更改。 之前你可以只需直接调用子工作流；而现在，需要在工作流中为要调用的父级定义触发器终结点。 通常，这意味着会添加类型为**手动**的触发器，随后在父定义中使用该触发器。 请注意，`host` 属性明确具有 `triggerName`，因为你必须始终指定所调用的触发器。

## <a name="other-changes"></a>其他更改
### <a name="new-queries-property"></a>新查询属性
所有操作类型现在都支持名为**查询**的新输入。 这可以结构化对象，而不必手动组合字符串。

### <a name="parse-function-renamed"></a>parse() 函数已重命名
随着我们将很快添加更多内容类型，`parse()` 函数已重命名为 `json()`。

## <a name="coming-soon-enterprise-integration-apis"></a>即将推出：企业集成 API
当前，我们尚未提供托管版本的企业集成 API（如 AS2）。 这些内容已涵盖在[路线图](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)中，即将推出。 同时，你可以通过 HTTP 操作使用现有的已部署 BizTalk API（如上面“使用已部署的 API 应用”所述）。




<!--HONumber=Jan17_HO3-->


