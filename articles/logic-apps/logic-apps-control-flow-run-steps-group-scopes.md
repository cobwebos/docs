---
title: "基于分组的操作状态运行步骤 - Azure 逻辑应用 | Microsoft Docs"
description: "将操作分组到作用域中并基于组状态运行步骤"
services: logic-apps
keywords: "分支, 并行处理"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 052af45962f442e96ca28f05ffaa1b9814b2588b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
# <a name="scopes-run-steps-based-on-group-status-in-logic-apps"></a>作用域：基于逻辑应用中的组状态运行步骤

若要仅在另一组操作成功或失败后运行步骤，请将该组放在一个“作用域”内。 如果你希望将各个操作组织为逻辑组，评估该组的状态并执行基于作用域状态的操作，则此结构非常有用。 当某个作用域中的所有操作都完成运行后，该作用域也确定了其自己的状态。 例如，当希望实现[异常和错误处理](../logic-apps/logic-apps-exception-handling.md#scopes)时可以使用作用域。 

若要检查作用域的状态，可以使用与用来确定逻辑应用运行状态（例如“已成功”、“已失败”、“已取消”，等等）的条件相同的条件。 默认情况下，当作用域的所有操作都成功时，作用域的状态将被标记为“已成功”。 但是，当作用域中有任何操作失败或被取消时，作用域的状态将被标记为“已失败”。 有关作用域的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

例如，下面是一个高级别逻辑应用，它使用作用域来运行特定操作并使用一个条件来检查作用域的状态。 如果作用域中有任何操作失败或意外终止，则作用域将被相应地标记为“已失败”或“已中止”，并且逻辑应用将发送一条“作用域已失败”消息。 如果作用域中的所有操作都成功，则逻辑应用会发送一条“作用域已成功”消息。

![设置“计划 - 定期”触发器](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>先决条件

若要完成本文中的示例，需要具有以下各项：

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 

* 一个由逻辑应用支持的任何电子邮件提供程序提供的电子邮件帐户。 此示例使用的是 Outlook.com。如果使用其他提供程序，则一般流程保持不变，但出现的 UI 会有所不同。

* 一个必应地图密钥。 若要获取此密钥，请参阅<a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Get a Bing Maps key</a>（获取必应地图密钥）。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

## <a name="create-sample-logic-app"></a>创建示例逻辑应用

首先，创建此示例逻辑应用，以便之后可以添加作用域：

![创建示例逻辑应用](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* 一个**计划 - 定期**触发器，用于按你指定的时间间隔检查必应地图服务
* 一个**必应地图 - 获取路线**操作，用于检查两个位置之间的旅行时间
* 一个条件语句，用于检查旅行时间是否超出了你指定的旅行时间。
* 一个在当前旅行时间超出指定时间时向你发送电子邮件的操作

你可以随时保存逻辑应用，因此请频繁保存你的工作。

1. 如果尚未登录到 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>，请进行登录。 创建空白逻辑应用。

2. 使用以下设置添加**计划 - 定期**触发器：**时间间隔** = 1 并且**频率** =“Minute”

   ![设置“计划 - 定期”触发器](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > 若要在视觉方面简化视图并在设计器中隐藏每个操作的详细信息，请在执行这些步骤时折叠每个操作的形状。

3. 添加“必应地图 - 获取路线”操作。 

   1. 如果尚没有必应地图连接，则会要求你创建一个连接。

      | 设置 | 值 | 说明 |
      | ------- | ----- | ----------- |
      | **连接名称** | BingMapsConnection | 提供连接的名称。 | 
      | **API 密钥** | <*your-Bing-Maps-key*> | 输入以前接收的必应地图密钥。 | 
      ||||  

   2. 根据此图像下方的表中所示设置**获取路线**操作：

      ![设置“必应地图 - 获取路线”操作](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      有关这些参数的详细信息，请参阅 [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx)（计算路线）。

      | 设置 | 值 | 说明 |
      | ------- | ----- | ----------- |
      | **路标 1** | <*起点*> | 输入路线的起点。 | 
      | **路标 2** | <*终点*> | 输入路线的目的地。 | 
      | **避免** | 无 | 输入路线上需要避免的项目，例如高速公路、收费站等。 有关可能的值，请参阅[计算路由](https://msdn.microsoft.com/library/ff701717.aspx)。 | 
      | **优化** | timeWithTraffic | 选择一个参数来优化路线，例如距离、时间（在使用当前交通信息的情况下），等等。 此示例使用以下值：timeWithTraffic | 
      | **距离单位** | <*your-preference*> | 输入要用来计算路线的距离单位。 此示例使用以下值：Mile | 
      | **旅行模式** | 驾车 | 输入路线的旅行模式。 此示例使用以下值：Driving | 
      | **运输日期/时间** | 无 | 仅适用于运输模式。 | 
      | **运输日期/时间类型** | 无 | 仅适用于运输模式。 | 
      ||||  

4. 添加一个条件，用以检查当前交通状况下的旅行时间是否超出了指定时间。 对于本例，请执行此图下的步骤：

   ![构建条件](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. 重命名条件并提供以下说明：**If traffic time more than specified time**

   2. 从参数列表中，选择“旅行期间 - 交通”字段（以秒为单位）。 

   3. 对于比较运算符，选择以下运算符：**大于**

   4. 对于比较值，输入 **600**，这是秒数，等于 10 分钟。

5. 在条件的 **If true** 分支中，为你的电子邮件提供程序添加一个“发送电子邮件”操作。 设置此操作的详细信息，如此图下的表中所示：

   ![向“If true”分支添加“发送电子邮件”操作](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. 对于“收件人”字段，输入你的电子邮件地址以进行测试。

   2. 对于“主题”字段，输入以下文本：

      ```Time to leave: Traffic more than 10 minutes```

   3. 对于“正文”字段，输入带尾随空格的以下文本： 

      ```Travel time: ```

      当光标出现在“正文”字段中时，动态内容列表将保持打开状态，以便你可以选择此时可用的任何参数。

   4. 在动态内容列表中，选择“表达式”。

   5. 查找并选择 **div( )** 函数。

   6. 在光标位于函数的括号内时，选择“动态内容”以便接下来可以添加“旅行期间 - 交通”参数。

   7. 在动态参数列表中的“获取路线”下，选择“旅行期间 - 交通”字段。

      ![选择“旅行期间 - 交通”](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. 在此字段解析为 JSON 格式后，添加一个**逗号** (```,```)，后跟数字 ```60```，以便将“旅行期间 - 交通”中的值从秒转换为分钟。 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      现在，表达式应如下例所示：

      ![完成表达式](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. 当完成时，确保选择“确定”。

  10. 在表达式解析后，添加带有前导空格的以下文本：``` minutes```
  
      “正文”字段现在如下例所示：

      ![完成的“正文”字段](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. 保存逻辑应用。

接下来，添加一个作用域，以便可以对特定操作进行分组并评估它们的状态。

## <a name="add-a-scope"></a>添加作用域

1. 如果尚未打开逻辑应用，则在逻辑应用设计器中打开逻辑应用。 

2. 在所需的工作流位置添加一个作用域。 例如：

   * 若要在逻辑应用工作流中的现有步骤之间添加作用域，请将指针移动到要添加作用域的箭头上方。 
   选择**加号** (**+**) >“添加作用域”。

     ![添加作用域](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     如果想要在工作流末尾添加条件，请在逻辑应用的底部选择“+ 新建步骤”>“...更多”>“添加作用域”。

3. 现在，添加要在作用域内运行的步骤，或拖动要在作用域内运行的现有步骤。 对于本例，请将以下操作拖动到作用域中：
      
   * **获取路线**
   * **If traffic time more than specified time**，这包括 **true** 和 **false** 分支

   现在，逻辑应用如下例所示：

   ![添加的作用域](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. 在作用域下，添加一个条件，用以检查作用域的状态。 重命名条件并提供以下说明：**If scope failed**

   ![添加用以检查作用域状态的条件](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. 构建以下表达式，用以检查作用域的状态是否等于 `Failed` 或 `Aborted`。

   ![添加用以检查作用域状态的表达式](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   或者，若要以文本形式输入此表达式，请选择“在高级模式下编辑”。

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. 在 **If true** 和 **If false** 分支中，添加要执行的操作，例如，发送电子邮件或消息。

   ![添加用以检查作用域状态的表达式](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. 保存逻辑应用。

已完成的逻辑应用现在看起来如下例所示，其中所有形状都已展开：

![带作用域的已完成逻辑应用](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>测试你的工作

在设计器工具栏上，选择“运行”。 如果作用域中的所有操作都成功，则会收到“作用域已成功”消息。 如果作用域中有任何操作未成功，则会收到“作用域已失败”消息。 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON 定义

如果是在代码视图中操作，可以改为在逻辑应用的 JSON 定义中定义作用域结构。 例如，下面是前面的逻辑应用中的触发器和操作的 JSON 定义：

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "None",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或者为其投票，请访问 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于条件运行步骤（条件语句）](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [基于不同的值运行步骤（switch 语句）](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行并重复执行步骤（循环）](../logic-apps/logic-apps-control-flow-loops.md)
* [运行或合并并行步骤（分支）](../logic-apps/logic-apps-control-flow-branches.md)