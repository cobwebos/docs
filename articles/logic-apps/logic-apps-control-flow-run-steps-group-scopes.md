---
title: 按范围分组和运行操作
description: 基于 Azure 逻辑应用中的组状态创建运行范围内的操作
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791487"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>使用 Azure 逻辑应用中的作用域基于组状态运行操作

若要仅在另一组操作成功或失败后运行操作，请在范围内对这些操作进行分组。 如果你希望将各个操作组织为逻辑组，评估该组的状态并执行基于作用域状态的操作，则此结构非常有用。 当某个作用域中的所有操作都完成运行后，该作用域也确定了其自己的状态。 例如，当希望实现[异常和错误处理](../logic-apps/logic-apps-exception-handling.md#scopes)时可以使用作用域。 

若要检查作用域的状态，可以使用与用来确定逻辑应用运行状态（例如“已成功”、“已失败”、“已取消”，等等）的条件相同的条件。 默认情况下，当作用域的所有操作都成功时，作用域的状态将被标记为“已成功”。 但是，当作用域中有任何操作失败或被取消时，作用域的状态将被标记为“已失败”。 有关作用域的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

例如，下面是一个高级别逻辑应用，它使用作用域来运行特定操作并使用一个条件来检查作用域的状态。 如果作用域中有任何操作失败或意外终止，则作用域将被相应地标记为“已失败”或“已中止”，并且逻辑应用将发送一条“作用域已失败”消息。 如果作用域中的所有操作都成功，则逻辑应用会发送一条“作用域已成功”消息。

![设置“计划 - 定期”触发器](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>必备组件

若要完成本文中的示例，需要具有以下各项：

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 

* 一个由逻辑应用支持的任何电子邮件提供程序提供的电子邮件帐户。 此示例使用的是 Outlook.com。 如果使用其他提供程序，则一般流程保持不变，但出现的 UI 会有所不同。

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

1. 使用以下设置添加**计划 - 定期**触发器：**时间间隔** = 1 并且**频率** =“Minute”

   ![设置“计划 - 定期”触发器](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > 若要在视觉方面简化视图并在设计器中隐藏每个操作的详细信息，请在执行这些步骤时折叠每个操作的形状。

1. 添加“必应地图 - 获取路线”操作。 

   1. 如果尚没有必应地图连接，则会要求你创建一个连接。

      | 设置 | Value | 描述 |
      | ------- | ----- | ----------- |
      | **连接名称** | BingMapsConnection | 提供连接的名称。 | 
      | **API 密钥** | <*your-Bing-Maps-key*> | 输入以前接收的必应地图密钥。 | 
      ||||  

   1. 根据此图像下方的表中所示设置**获取路线**操作：

      ![设置“必应地图 - 获取路线”操作](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      有关这些参数的详细信息，请参阅 [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx)（计算路线）。

      | 设置 | Value | 描述 |
      | ------- | ----- | ----------- |
      | **路标 1** | <*起点*> | 输入路线的起点。 | 
      | **路标 2** | <*终点*> | 输入路线的目的地。 | 
      | **避免** | None | 输入路线上需要避免的项目，例如高速公路、收费站等。 有关可能的值，请参阅[计算路由](https://msdn.microsoft.com/library/ff701717.aspx)。 | 
      | **优化** | timeWithTraffic | 选择一个参数来优化路线，例如距离、时间（在使用当前交通信息的情况下），等等。 此示例使用以下值：timeWithTraffic | 
      | **距离单位** | <*your-preference*> | 输入要用来计算路线的距离单位。 此示例使用以下值：Mile | 
      | **旅行模式** | 驾车 | 输入路线的旅行模式。 此示例使用以下值：Driving | 
      | **运输日期/时间** | None | 仅适用于运输模式。 | 
      | **运输日期/时间类型** | None | 仅适用于运输模式。 | 
      ||||  

1. [添加条件](../logic-apps/logic-apps-control-flow-conditional-statement.md)，检查当前交通状况下的旅行时间是否超出了指定时间。 
   对于本示例，请遵循以下步骤：

   1. 重命名条件并提供以下说明：交通时间是否超过指定时间

   1. 在最左侧的列中，单击“选择值”框内部，会显示动态内容列表。 从该列表中，选择“旅行期间 - 交通”字段（以秒为单位）。 

      ![构建条件](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. 在中间的框中选择此运算符：大于

   1. 在最右边的列中，输入此比较值（以秒为单位），相当于10分钟： **600**

      完成后，条件如以下示例所示：

      ![已完成的条件](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. 在“If true”分支中，为电子邮件提供程序添加一个“发送电子邮件”操作。 
   按照此图像下的步骤设置此操作：

   ![向“If true”分支添加“发送电子邮件”操作](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. 在“收件人”字段中，输入电子邮件地址以进行测试。

   1. 在“主题”字段中，输入以下文本：

      ```Time to leave: Traffic more than 10 minutes```

   1. 在“正文”字段中，输入带尾随空格的以下文本： 

      ```Travel time:```

      当光标出现在“正文”字段中时，动态内容列表将保持打开状态，以便你可以选择此时可用的任何参数。

   1. 在动态内容列表中，选择“表达式”。

   1. 查找并选择“div()”函数。 
      将游标置于函数的括号内。

   1. 游标位于函数的括号内时，选择“动态内容”，以便显示动态内容列表。 
   
   1. 在“获取路线”部分，选择“旅行期间 - 交通”字段。

      ![选择“旅行期间 - 交通”](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. 在此字段解析为 JSON 格式后，添加一个**逗号** (```,```)，后跟数字 ```60```，以便将“旅行期间 - 交通”中的值从秒转换为分钟。 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      现在，表达式应如下例所示：

      ![完成表达式](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. 完成后，选择“确定”。

   <!-- markdownlint-disable MD038 -->
   1. 表达式解析后，添加以下带前导空格的文本： ``` minutes```
  
       “正文”字段现在如下例所示：

       ![完成的 "正文" 字段](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. 保存逻辑应用。

接下来，添加一个作用域，以便可以对特定操作进行分组并评估它们的状态。

## <a name="add-a-scope"></a>添加作用域

1. 如果尚未打开逻辑应用，则在逻辑应用设计器中打开逻辑应用。 

1. 在所需的工作流位置添加一个作用域。 例如，若要在逻辑应用工作流中的现有步骤之间添加范围，请按照下列步骤操作： 

   1. 将指针移到要添加范围的箭头上。 
   选择“加号”(+) >“添加操作”。

      ![添加作用域](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. 在搜索框中，输入“范围”作为筛选器。 
   选择“范围”操作。

## <a name="add-steps-to-scope"></a>将步骤添加到范围

1. 现在，添加要在作用域内运行的步骤，或拖动要在作用域内运行的现有步骤。 对于本例，请将以下操作拖动到作用域中：
      
   * **获取路线**
   * 交通时间是否超过指定时间，包括“true”和“false”分支

   现在，逻辑应用如下例所示：

   ![添加的作用域](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. 在作用域下，添加一个条件，用以检查作用域的状态。 重命名条件并提供以下说明：**If scope failed**

   ![添加用以检查作用域状态的条件](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. 在此条件下，添加这些表达式，检查范围的状态是否等于“失败”或“已中止”。 

   1. 要添加其他行，请选择“添加”。 

   1. 在每一行中，单击左侧框内部，以显示动态内容列表。 
   在动态内容列表中，选择“表达式”。 在编辑框中，输入此表达式，然后选择“确定”： 
   
      `result('Scope')[0]['status']`

      ![添加用以检查作用域状态的表达式](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. 对于这两行，选择“等于”作为其运算符。 
   
   1. 对于比较值，在第一行中输入 `Failed`。 
   在第二行中，输入 `Aborted`。 

      完成后，条件如以下示例所示：

      ![添加用以检查作用域状态的表达式](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      现在，设置条件的 `runAfter` 属性，以便条件检查范围状态并运行在后续步骤中定义的匹配操作。

   1. 在“范围是否失败”条件中，选择“省略号”(...) 按钮，然后选择“配置在之后运行”。

      ![配置“runAfter”属性](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. 选择所有这些范围状态：“成功”、“失败”、“跳过”、“已超时”

      ![选择范围状态](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. 完成后，选择“完成”。 
   该条件现在显示“信息”图标。

1. 在“If true”和“If false”分支中，根据每个范围状态添加要执行的操作，例如，发送电子邮件或消息。

   ![根据范围状态添加要采取的操作](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. 保存逻辑应用。

已完成的逻辑应用如下例所示：

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
    }
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
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
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
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
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
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
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
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
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
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或者为其投票，请访问 [Azure 逻辑应用用户反馈站点](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于条件运行步骤（条件语句）](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [基于不同的值运行步骤（switch 语句）](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行并重复执行步骤（循环）](../logic-apps/logic-apps-control-flow-loops.md)
* [运行或合并并行步骤（分支）](../logic-apps/logic-apps-control-flow-branches.md)
