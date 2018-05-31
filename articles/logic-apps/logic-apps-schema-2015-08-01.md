---
title: 架构更新（2015 年 8 月 1 日预览版）- Azure 逻辑应用 | Microsoft 文档
description: 为架构版本为 2015-08-01-preview 的 Azure 逻辑应用创建 JSON 定义
author: stepsic-microsoft-com
manager: SyntaxC4
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 05/31/2016
ms.author: stepsic; LADocs
ms.openlocfilehash: bdadc2e33082421500f21d5926ac1e660f4164d4
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32774295"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Azure 逻辑应用的架构更新 - 2015 年 8 月 1 日预览版

适用于 Azure 逻辑应用的这个架构和 API 版本包含多项关键改进，使逻辑应用更可靠且更易于使用：

* “APIApp”操作类型现在名为“[APIConnection](#api-connections)”。
* “Repeat”操作现在名为“[Foreach](#foreach)”。
* 不再需要 [**HTTP 侦听器** API 应用](#http-listener)。
* 调用子工作流会使用[新架构](#child-workflows)。

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>移到 API 连接

最大的变化是不再需要将 API 应用部署到 Azure 订阅，因此可使用 API。 以下是可使用 API 的方式：

* 托管 API
* 自定义 Web API

每种方法的处理方式略有不同，因为其管理和托管模型不同。 此模型的一个优点是不再局限于 Azure 资源组中部署的资源。 

### <a name="managed-apis"></a>托管 API

Microsoft 代表你管理某些 API，例如 Office 365、Salesforce、Twitter 和 FTP。 某些托管 API 可以按原样使用（例如必应翻译），但另一些 API 需要进行配置，也称为连接。

例如，在使用 Office 365 时，必须创建包括 Office 365 登录令牌的连接。 令牌以安全方式存储和刷新，以便逻辑应用可以随时调用 Office 365 API。 如果要连接到 SQL 或 FTP 服务器，则必须创建具有连接字符串的连接。 

在此定义中，这些操作名为 `APIConnection`。 下面是调用 Office 365 以发送电子邮件的连接的示例：

``` json
{
   "actions": {
      "Send_an_email": {
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
            "method": "POST",
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

`host` 对象是特定于 API 连接的输入的一部分，并包含以下部分：`api` 和 `connection`。 `api` 对象为承载该托管 API 的位置指定运行时 URL。 可以通过调用 `GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview` 来查看所有可用的托管 API。

使用 API 时，该 API 可以定义也可以不定义任何连接参数。 因此，如果 API 未定义这些参数，则不需要任何连接。 如果 API 定义了这些参数，则必须创建含有指定名称的连接。  
然后，可以在 `host` 对象内的 `connection` 对象中引用该名称。 若要在资源组中创建连接，请调用此方法：

```
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

使用以下正文：

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>在 Azure 资源管理器模板中部署托管 API

只要不要求进行交互式登录，便可以在 Azure 资源管理器模板中创建完整应用。
如果要求登录，则可以使用 Azure 资源管理器模板执行所有设置，但还必须访问 Azure 门户来对连接进行授权。 

``` json
"resources": [ {
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
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
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
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
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
         "outputs": {}
      }
   }
} ]
```

在此示例中可以看到，连接只是位于资源组中的资源。 它们引用订阅中可供你使用的托管 API。

### <a name="your-custom-web-apis"></a>自定义 Web API

如果使用自己的 API（而不是 Microsoft 托管的 API），则应使用内置 **HTTP** 操作调用它们。 若要获得理想体验，应该为 API 公开一个 Swagger 终结点。 此终结点使逻辑应用设计器可以呈现 API 的输入和输出。 如果没有 Swagger，则设计器只能将输入和输出显示为不透明的 JSON 对象。

下面是演示新 `metadata.apiDefinitionUrl` 属性的示例：

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

如果将 Web API 托管在 Azure 应用服务中，则 Web API 会自动显示在设计器中提供的操作列表中。 如果未自动显示，则必须直接粘贴在 URL 中。 Swagger 终结点必须未经身份验证才能在逻辑应用设计器中使用（不过可以使用 Swagger 中支持的任何方法保护 API 本身）。

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>使用 2015-08-01-preview 调用已部署的 API 应用

如果以前部署了 API 应用，则可以通过 HTTP 操作调用该应用。
例如，如果使用 Dropbox 列出文件，则 **2014-12-01-preview** 架构版本定义可能具有与下面类似的内容：

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
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
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

现在，可以构建类似以下示例的等效 HTTP 操作，同时保持逻辑应用定义的参数部分不变：

``` json
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
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

逐个演练这些属性：

| 操作属性 | 说明 |
| --- | --- |
| `type` | 是 `Http` 而不是 `APIapp` |
| `metadata.apiDefinitionUrl` | 若要在逻辑应用设计器中使用此操作，请包括元数据终结点，该终结点是通过以下方式构造的：`{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard`。 |
| `inputs.uri` | 构造方式：`{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | 始终是 `POST` |
| `inputs.body` | 等同于 API 应用参数 |
| `inputs.authentication` | 等同于 API 应用身份验证 |

此方法应适用于所有 API 应用操作。 但是请记住，以前的这些 API 应用不再受支持。 因此，应移到以前的其他两个选项（托管 API 或托管自定义 Web API）之一。

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>已将“repeat”重命名为“foreach”

对于以前的架构版本，很多客户反馈说，“Repeat”操作名称让人困惑，并且没有正确地表达“Repeat”实际上是一个 for-each 循环。 因此，我们将 `repeat` 重命名为 `foreach`。 以前，你会如下例所示编写此操作：

``` json
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
```

现在，需改为这样编写：

``` json
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
```

此外，`repeatItem()` 函数现已重命名为 `item()`，该函数引用当前迭代期间循环处理的项。 

### <a name="reference-outputs-from-foreach"></a>引用“foreach”的输出

为简化操作，不再将 `foreach` 操作的输出包装在名为 `repeatItems` 的对象中。 此外，由于进行了这些更改，删除了 `repeatItem()`、`repeatBody()` 和 `repeatOutputs()` 函数。

因此，使用之前的 `repeat` 示例，将获取这些输出：

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

现在，则会收到这些输出：

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"http://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

以前，引用这些输出时，若要从操作获取 `body`，需要：

``` json
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
```

现在可改用此版本：

``` json
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
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>本机 HTTP 侦听器

HTTP 侦听器功能现在已内置。 因此，不再需要部署 HTTP 侦听器 API 应用。 在此处了解[有关如何使逻辑应用终结点可调用的完整详细信息](../logic-apps/logic-apps-http-endpoint.md)。 

由于进行了这些更改，我们已删除 `@accessKeys()` 函数，并将它替换为 `@listCallbackURL()` 函数以便用于获取终结点（如果必要）。 此外，现在必须在逻辑应用中至少定义一个触发器。 如果要对工作流执行 `/run` 操作，则必须具有 `manual`、`apiConnectionWebhook` 或 `httpWebhook` 触发器之一。

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>调用子工作流

以前，调用子工作流需要转到该工作流，获取访问令牌，然后将令牌粘贴到要调用该子工作流的逻辑应用定义中。 借助新的架构，逻辑应用引擎会在运行时自动为子工作流生成 SAS，因此不必将任何密码粘贴到定义中。 下面是一个示例：

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
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

最后，需要对子工作流进行更改。 之前可以直接调用子工作流；而现在，必须在工作流中为要调用的父级定义触发器终结点。 通常，会添加类型为 `manual` 的触发器，并在父定义中使用该触发器。 请注意，`host` 属性明确具有 `triggerName`，你必须始终指定所调用的触发器。

## <a name="other-changes"></a>其他更改

### <a name="new-queries-property"></a>新“queries”属性

所有操作类型现在都支持名为 `queries` 的新输入。 此输入可以是一个结构化对象，而不必手动组合字符串。

### <a name="renamed-parse-function-to-json"></a>已将“parse()”函数重命名为“json()”

我们即将添加更多内容类型，因此已将 `parse()` 函数重命名为 `json()`。

## <a name="coming-soon-enterprise-integration-apis"></a>即将推出：企业集成 API

我们尚没有托管版本的企业集成 API，如 AS2。 同时，可以通过 HTTP 操作使用现有的已部署 BizTalk API。 有关详细信息，请参阅[集成路线图](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)中的“使用已部署的 API 应用”。 
