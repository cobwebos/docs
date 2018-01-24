---
title: "在工作流是创建循环和范围或者解除批处理数据 - Azure 逻辑应用 | Microsoft 文档"
description: "创建循环来迭代数据，将操作分组到范围，或者解除批处理数据，以便在 Azure 逻辑应用中启动更多工作流。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 75b52eeb-23a7-47dd-a42f-1351c6dfebdc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 9cdbe4a12a0b16341a1e52f176901045baf327b5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="logic-apps-loops-scopes-and-debatching"></a>逻辑应用循环、范围和解除批处理
  
逻辑应用提供了多种方式处理工作流中的数组、集合、批和循环。
  
## <a name="foreach-loop-and-arrays"></a>ForEach 循环和数组
  
借助逻辑应用可循环访问一组数据并对每个项执行操作。  循环访问集合是通过 `foreach` 操作实现的。  在设计器中，可以添加 foreach 循环。  选择要循环访问的数组之后，可以开始添加操作。  对于每个 foreach 循环，可以添加多个操作。  处于循环中之后，可以开始指定对数组的每个值应执行的操作。

  此示例向包含“microsoft.com”的每个电子邮件地址发送一封电子邮件。 如果使用代码视图，可以指定 for each 循环，如以下示例所示：

``` json
{
    "email_filter": {
        "type": "query",
        "inputs": {
            "from": "@triggerBody()['emails']",
            "where": "@contains(item()['email'], 'microsoft.com')"
        }
    },
    "forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "actions": {
            "send_email": {
                "type": "ApiConnection",
                "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                },
                }
            }
        },
        "runAfter":{
            "email_filter": [ "Succeeded" ]
        }
    }
}
```
  
  `foreach` 操作可以循环访问数千个实体的数组。  默认情况下，并行执行迭代。  有关数组和并发限制的详细信息，请参阅[限制和配置](logic-apps-limits-and-config.md)。

### <a name="sequential-foreach-loops"></a>顺序 ForEach 循环

若要使 foreach 循环按顺序执行，应添加 `Sequential` 操作选项。

``` json
"forEach_email": {
        "type": "foreach",
        "foreach": "@body('email_filter')",
        "operationOptions": "Sequential",
        "..."
}
```
  
## <a name="until-loop"></a>Until 循环
  
  可以执行一个或一系列操作，直到满足某个条件。  使用 until 循环的最常见情况是调用终结点，直到获取所需响应。  在设计器中，可以指定添加 until 循环。  在循环中添加操作之后，可以设置退出条件以及循环限制。
  
  此示例调用 HTTP 终结点，直到响应正文包含值“Completed”。  它在以下任一情况下完成： 
  
  * HTTP 响应的状态是“Completed”
  * 已尝试了 1 小时
  * 循环了 100 次
  
  如果使用代码视图，可以指定 until 循环，如以下示例所示：
  
  ``` json
  {
      "until_successful":{
        "type": "until",
        "expression": "@equals(actions('http')['status'], 'Completed')",
        "limit": {
            "count": 100,
            "timeout": "PT1H"
        },
        "actions": {
            "create_resource": {
                "type": "http",
                "inputs": {
                    "url": "http://provisionRseource.com",
                    "body": {
                        "resourceId": "@triggerBody()"
                    }
                }
            }
        }
      }
  }
  ```
  
## <a name="spliton-and-debatching"></a>SplitOn 和解除批处理

有时触发器可能会收到要解除批处理并对每个项启动工作流的项数组。  此解除批处理操作可以通过 `spliton` 命令来实现。  默认情况下，如果触发器 swagger 指定作为数组的有效负载，则会添加 `spliton`。 `spliton` 命令对数组中的每一项启动运行。  SplitOn 只能添加到可以手动配置或重写的触发器。 不能具有 `spliton` 且不能实现同步响应模式。  调用的具有 `response` 操作以及 `spliton` 的任何工作流都会异步运行，并发送即时 `202 Accepted` 响应。  

  此示例会接收包含多个项的数组并在每行上解除批处理。 SplitOn 可以在代码视图中指定，如以下示例所示：

```
{
    "myDebatchTrigger": {
        "type": "Http",
        "inputs": {
            "url": "http://getNewCustomers",
        },
        "recurrence": {
            "frequencey": "Second",
            "interval": 15
        },
        "spliton": "@triggerBody()['rows']"
    }
}
```

## <a name="scopes"></a>作用域

可以使用范围将一系列操作分组在一起。  作用域对于实现异常处理很有用。  在设计器中，可以添加新范围，并开始在其内部添加任何操作。  可以在代码视图中定义作用域，如以下示例所示：


```
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```
