---
title: "创作逻辑应用定义 |Microsoft Docs"
description: "了解如何编写逻辑应用的 JSON 定义"
author: jeffhollan
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 253459f431c43b7018fb2a70a966f7652d3f327f


---
# <a name="author-logic-app-definitions"></a>创作逻辑应用定义
本主题演示如何使用 [Azure 逻辑应用](app-service-logic-what-are-logic-apps.md)定义语言，这是一种简单的声明性 JSON 语言。 如果尚未创建，请首先查阅[如何创建新的逻辑应用](app-service-logic-create-a-logic-app.md)。 你还可以阅读 [MSDN 上的定义语言的完整参考资料](http://aka.ms/logicappsdocs)。

## <a name="several-steps-that-repeat-over-a-list"></a>对列表重复执行的几个步骤
可以利用 [foreach 类型](app-service-logic-loops-and-scopes.md)遍历多达 10k 个项的数组，对每个项执行一个操作。

## <a name="a-failure-handling-step-if-something-goes-wrong"></a>出现问题时的失败处理步骤
当**且仅当**一个或多个调用失败时，通常想要编写*修复步骤* — 执行的某种逻辑。 在本示例中，从多个位置获取数据，但是如果调用失败，我想在某处发布一条消息，以便以后可以跟踪到此故障：  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "triggers": {
        "manual": {
            "type": "manual"
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
                "readData": ["Failed"]
            }
        }
    },
    "outputs": {}
}
```

可以使用 `runAfter` 属性来指定仅当 `readData` 为 **Failed** 时运行 `postToErrorMessageQueue`。  此属性值也可以是一个可能值列表，因此 `runAfter` 可以为 `["Succeeded", "Failed"]`。

最后，因为已处理错误，不再需要将运行标记为 **Failed**。 正如在此处看到的，即使有一个步骤失败，此运行也为 **Succeeded**，因为我写了步骤来处理此失败。

## <a name="two-or-more-steps-that-execute-in-parallel"></a>并行执行的两个（或更多）步骤
若要让多个操作并行执行，运行时的 `runAfter` 属性必须相同。 

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {
        "manual": {
            "type": "manual"
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
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        },
        "branch2": {
            "type": "Http",
            "inputs": "...",
            "runAfter": {
                "readData": ["Succeeded"]
            }
        }
    },
    "outputs": {}
}
```

正如以上示例所示，`branch1` 和 `branch2` 均设置为在 `readData` 之后运行。 因此，这两个分支将并行运行：

![并行](./media/app-service-logic-author-definitions/parallel.png)

可以看到这两个分支的时间戳完全相同。 

## <a name="join-two-parallel-branches"></a>联接两个并行分支
可以通过向与上面类似的 `runAfter` 属性中添加项来联接设为并行执行的两个操作。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
    "actions": {
        "readData": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        },
        "branch1": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "branch2": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {
                "readData": [
                    "Succeeded"
                ]
            },
            "type": "Http"
        },
        "join": {
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
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {},
    "parameters": {},
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {}
            },
            "kind": "Http",
            "type": "Request"
        }
    }
}
```

![并行](./media/app-service-logic-author-definitions/join.png)

## <a name="mapping-items-in-a-list-to-some-different-configuration"></a>将列表中的项映射到某些不同的配置
接下来，假设我们想要获取完全不同的内容，该内容取决于一个属性的值。 我们可以创建值到目标的映射，并将其作为参数。  

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
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
        "manual": {
            "type": "manual"
        }
    },
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
            },
            "conditions": [{
                "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
            }],
            "forEach": "@body('getArticles').responseData.feed.entries"
        }
    }
}
```

在本例中，首先获取文章列表，然后根据定义为参数的类别在映射中查找要从中获取内容的 URL。 

此处要注意以下两项：[`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 函数用于查看类别是否与定义的已知类别之一相符。 第二个，在获取类别后，可以使用方括号提取映射的项：`parameters[...]`。 

## <a name="working-with-strings"></a>操作字符串
有多种函数可用于操作字符串。 举个例子，我们要将一个字符串传递给系统，但是我们不确信是否可以正确处理字符编码。 一种选择是对字符串进行 base64 编码。 但是，为了避免在 URL 中转义我们要替换几个字符。 

我们还想要订购方名称的子字符串，因为不使用前 5 个字符。

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {
        "manual": {
            "type": "manual"
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

从内到外进行处理：

1. 获取订购方名称的 [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)，这可以返回字符总数。
2. 减 5（因为想要更短的字符串）
3. 实际使用的是 [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring)。 我们从索引处 `5` 开始，提取字符串的其余部分。
4. 将此子字符串转换为 [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 字符串
5. 将所有 `+` 字符 [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 为 `-`
6. 将所有 `/` 字符 [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 为 `_`

## <a name="working-with-date-times"></a>使用日期时间
日期时间可能很有用，特别是尝试从不支持**触发器**的数据源中提取数据的时候。  你还可以使用日期时间找出各个步骤需要花费的时间。 

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

在本例中，我们提取前一步的 `startTime`。 然后获取当前时间，并减去一秒：[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds)（可以使用其他时间单位，如 `minutes` 或 `hours`）。 最后，比较这两个值。 如果第一个值小于第二个值，则意味着从首次下订单开始过去了超过 1 秒。 

此外请注意，可以使用字符串格式化函数设置日期的格式：在查询字符串中我使用 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow) 获取 RFC1123。 所有日期格式在 [MSDN 中均有记录](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)。 

## <a name="using-deployment-time-parameters-for-different-environments"></a>针对不同的环境使用部署时间参数
在具有开发环境、过渡环境和生产环境的地方普遍具有部署生命周期。 例如，在所有这些环境中你可能想要相同的定义，但是使用不同数据库。 同样，你可能想要在许多不同区域中使用相同的定义，以实现高可用性，但是希望每个逻辑应用实例与该区域的数据库通信。 

请注意，这不同于在*运行时*采用不同参数，为此应使用上面所调用的 `trigger()` 函数。 

可以首先编写一个非常简单的定义，如下：

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
        "manual": {
            "type": "manual"
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

然后，在逻辑应用的实际 `PUT` 请求中提供参数 `uri`。 请注意，由于不再存在默认值，因此在逻辑应用有效负载中需要此参数：

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

在每个环境中可对 `connection` 参数提供不同值。 

有关用于创建和管理逻辑应用的所有选项，请参阅 [REST API 文档](https://msdn.microsoft.com/library/azure/mt643787.aspx)。 




<!--HONumber=Nov16_HO3-->


