---
title: "使用 JSON 定义工作流 - Azure 逻辑应用 | Microsoft 文档"
description: "如何在 JSON 中为逻辑应用编写工作流定义"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7f9e5a10066df8a464c285273e77a85c0d562ebb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>使用 JSON 为逻辑应用创建工作流定义

可以使用简单的声明性 JSON 语言为 [Azure 逻辑应用](logic-apps-what-are-logic-apps.md)创建工作流定义。 首先请查看[如何使用逻辑应用设计器创建第一个逻辑应用](logic-apps-create-a-logic-app.md)（如果尚未这样做）。 另请参阅[工作流定义语言参考大全](http://aka.ms/logicappsdocs)。

## <a name="repeat-steps-over-a-list"></a>针对一个列表重复执行步骤

若要循环访问多达 10,000 个项的数组并对每个项执行一个操作，请使用 [foreach 类型](logic-apps-loops-and-scopes.md)。

## <a name="handle-failures-if-something-goes-wrong"></a>在出现故障时处理故障

通常需包含*修复步骤* — *当且仅当* 一个或多个调用失败时执行的某种逻辑。 本示例从多个位置获取数据，但如果调用失败，则需在某处发布一条消息，方便以后跟踪该失败：  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

例如，若要指定 `postToErrorMessageQueue` 仅在 `readData` `Failed`后运行，请使用 `runAfter` 属性指定一系列可能的值，使 `runAfter` 可以为 `["Succeeded", "Failed"]`。

最后，因为此示例现在可处理错误，我们不再将运行标记为 `Failed`。 由于我们已在此示例中添加该步骤来处理此故障，运行 `Succeeded`，虽然一个步骤 `Failed`。

## <a name="execute-two-or-more-steps-in-parallel"></a>并行执行两个或更多个步骤

若要并行运行多个操作，运行时的 `runAfter` 属性必须相同。 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

在此示例中，`branch1` 和 `branch2` 均设置为在 `readData` 后运行。 因此，这两个分支并行运行。 这两个分支的时间戳完全相同。

![并行](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>联接两个并行分支

可以像前面的示例一样向 `runAfter` 属性添加项，以便将设置为并行运行的两个操作联接起来。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![并行](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>将列表项映射到其他配置

接下来，假设我们需要根据属性的值获取不同的内容。 我们可以创建值到目标的映射，并将其作为参数。  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

在这种情况下，我们首先获取文章的列表。 第二步使用映射查找获取内容所需的 URL，具体取决于作为参数定义的类别。

请注意： 

*   [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 函数检查类别是否与某个已知的已定义类别匹配。

*   在获取类别后，可以使用方括号提取映射的项：`parameters[...]`

## <a name="process-strings"></a>处理字符串

可以使用各种函数来操作字符串。 例如，假设我们需要将一个字符串传递给某个系统，但不确定如何正确处理才能进行字符编码。 一种选择是对字符串进行 base64 编码。 但为了避免在 URL 中转义，我们要替换几个字符。 

我们还想要订购方名称的子字符串，因为不使用前五个字符。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

从内到外处理：

1. 获取订购方名称的 [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)，这可以返回字符总数。

2. 减 5（因为想要更短的字符串）。

3. 实际使用的是 [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring)。 我们从索引处 `5` 开始，提取字符串的其余部分。

4. 将此子字符串转换为 [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 字符串。

5. 将所有 `+` 字符 [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 为 `-` 字符。

6. 将所有 `/` 字符 [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 为 `_` 字符。

## <a name="work-with-date-times"></a>使用日期时间

日期时间可能很有用，特别是尝试从不支持*触发器* 的数据源中提取数据的时候。 还可以使用日期时间找出各个步骤需要花费的时间。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

在本例中，我们提取前一步的 `startTime`。 然后，我们获取当前时间，并从中减去 1 秒：

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

可以使用其他时间单位，例如`minutes`或`hours`。 最后，比较这两个值。 如果第一个值小于第二个值，则意味着从首次下订单开始过去了 1 秒以上的时间。

若要设置日期格式，可以使用字符串格式化程序。 例如，若要获取 RFC1123，可以使用 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)。 若要了解日期格式设置，请参阅 [Workflow Definition Language](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)（工作流定义语言）。

## <a name="deployment-parameters-for-different-environments"></a>适用于不同环境的部署参数

通常情况下，部署生命周期涉及开发环境、过渡环境和生产环境。 例如，用户可以将同一定义用于所有这些环境，但使用不同的数据库。 同样，用户可能需要在不同区域中使用同一定义以实现高可用性，但又需要每个逻辑应用实例与该区域的数据库通信。
这种情况不同于在*运行时*使用参数，后一情况应像前面的示例一样改用 `trigger()` 函数。

可以首先编写一个基本的定义，如以下示例所示：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

在逻辑应用的实际 `PUT` 请求中，可以提供参数 `uri`。 默认值不再存在，因此逻辑应用有效负载需要以下参数：

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

在每个环境中，可为 `connection` 参数提供不同值。 

如需用于创建和管理逻辑应用的所有选项，请参阅 [REST API 文档](https://msdn.microsoft.com/library/azure/mt643787.aspx)。 
