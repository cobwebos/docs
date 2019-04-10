---
title: 添加循环重复执行操作或处理数组 - Azure 逻辑应用 | Microsoft Docs
description: 如何在 Azure 逻辑应用中创建重复执行工作流操作或处理数组的循环
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: 3faa3b0a5cd919752f8b7e4969e3affd668c8077
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360766"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>在 Azure 逻辑应用中添加循环以重复执行操作或处理数组

若要在逻辑应用中处理数组，可以创建[“Foreach”循环](#foreach-loop)。 此循环会对数组中的每一项重复一个或多个操作。 有关“Foreach”循环可处理数组项数的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

若要重复操作直到满足条件或状态发生变化，可以创建[“Until”循环](#until-loop)。 逻辑应用先运行在循环中，所有操作，然后检查的条件或状态。 如果满足该条件，则循环将停止。 否则，循环将继续进行。 有关逻辑应用运行中“Until”循环数的限制，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

> [!TIP]
> 如果你有接收数组的触发器并且希望针对每个数组项运行工作流，则可以使用 [**SplitOn** 触发器属性](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)“分离”该数组。 

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Foreach 循环

“Foreach 循环”在每个数组项上重复一个或多个操作，且仅在数组上工作。 “Foreach”循环中的迭代并行运行。 但是，可以通过设置[顺序“Foreach”循环](#sequential-foreach-loop)一次运行一次迭代。 

使用“Foreach”循环时请注意以下事项：

* 在嵌套循环中，迭代总是按顺序运行，而不是并行运行。 若要对嵌套循环中的项目并行运行操作，请创建并[调用子逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。

* 若要在每次循环迭代期间从变量操作获得可预测的结果，请按顺序运行这些循环。 例如，当并发运行的循环结束时，递增变量、递减变量和附加到变量操作会返回可预测的结果。 但是，在并发运行循环的每次迭代期间，这些操作可能会返回不可预测的结果。 

* “Foreach”循环中的操作使用 [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
表达式来引用和处理数组中的每个项。 如果指定了不在数组中的数据，则逻辑应用工作流将失败。 

此示例逻辑应用会发送网站 RSS 源的每日摘要。 该应用使用“Foreach”循环，为每一个新项发送电子邮件。

1. 使用 Outlook.com 或 Office 365 Outlook 帐户[创建此示例逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

2. 在 RSS 触发器与“发送电子邮件”操作之间，添加一个 Foreach 循环。 

   1. 若要在步骤之间添加循环，请将鼠标指针移到这些步骤之间的箭头上。 
   选择出现的加号 (+)，然后选择“添加操作”。

      ![选择“添加操作”。](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. 在搜索框下，选择“所有”。 在搜索框中键入“for each”作为筛选器。 在操作列表中选择此操作：“For each - 控制”

      ![添加“For each”循环](media/logic-apps-control-flow-loops/select-for-each.png)

3. 现在，构建循环。 在“添加动态内容”列表出现后，在“选择来自之前步骤的输出”下，选择“源链接”数组，这是来自 RSS 触发器的输出。 

   ![从动态内容列表进行选择](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 只能选择来自之前步骤的数组输出。

   所选数组现在显示在此处：

   ![选择数组](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 若要对每个数组项执行操作，请将“发送电子邮件”操作拖到循环中。 

   你的逻辑应用可能看起来如以下示例所示：

   ![向 Foreach 循环添加步骤](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. 保存逻辑应用。 若要手动测试逻辑应用，请在设计器工具栏上选择“运行”。

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Foreach 循环定义 (JSON)

如果是在逻辑应用的代码视图中操作，可以改为在逻辑应用的 JSON 定义中定义 `Foreach` 循环，例如：

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>“Foreach”循环：顺序

默认情况下，“Foreach”循环中的周期并行运行。 若要按顺序运行每个周期，请设置循环的“顺序”选项。 如果期望得出可预测结果的循环中具有嵌套循环或变量时，“Foreach”循环必须按顺序运行。 

1. 在循环的右上角，选择**省略号** (**...**) >“设置”。

   ![在 Foreach 循环中，选择“...”>“设置”](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. 在“并发控制”下，将“并发控制”设置转为“开启”。 将“并行度”滑块移至“1”，然后选择“完成”。

   ![启用并发控制](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

如果在使用逻辑应用的 JSON 定义，则可以通过添加 `operationOptions` 参数来使用 `Sequential` 选项，例如：

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Until 循环
  
若要运行并重复执行操作，直到获取满足某个条件或状态更改，请将这些操作置于 Until 循环。 首先，在逻辑应用运行在循环中，所有操作，，然后检查的条件或状态。 如果满足该条件，则循环将停止。 否则，循环将继续进行。

下面是可以在其中使用“Until”循环的一些常见场景：

* 调用某个终结点，直至获得想要的响应。

* 在数据库中创建记录。 等待该记录中的特定字段获得批准。 继续处理。 

在每天上午 8:00，此示例逻辑应用都递增某个变量的值，直到该变量的值等于 10。 然后，该逻辑应用会发送一封电子邮件来确认当前值。 

> [!NOTE]
> 这些步骤使用 Office 365 Outlook，但也可以使用逻辑应用支持的任何电子邮件提供商。 
> [检查此处的连接器列表](https://docs.microsoft.com/connectors/)。 如果使用其他电子邮件帐户，则常规步骤保持不变，但 UI 外观可能稍有不同。 

1. 创建空白逻辑应用。 在逻辑应用设计器的搜索框下，选择“全部”。 搜索“定期”。 
   从触发器列表中选择此触发器：“定期 - 计划”

   ![添加“定期 - 计划”触发器](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. 通过设置时间间隔、频率和天中的小时来指定触发器何时触发。 若要设置小时，请选择“显示高级选项”。

   ![设置定期计划](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | 属性 | 值 |
   | -------- | ----- |
   | **时间间隔** | 1 | 
   | **频率** | 日期 |
   | **在这些小时** | 8 |
   ||| 

1. 在触发器下，选择“新建步骤”。 
   搜索“变量”，然后选择以下操作：“初始化变量 - 变量”

   ![添加“初始化变量 - 变量”操作](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. 使用以下值设置变量：

   ![设置变量属性](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | 属性 | 值 | 描述 |
   | -------- | ----- | ----------- |
   | **名称** | 限制 | 变量的名称 | 
   | **Type** | Integer | 变量的数据类型 | 
   | **值** | 0 | 变量的起始值 | 
   |||| 

1. 在“初始化变量”操作下，选择“新建步骤”。 

1. 在搜索框下，选择“所有”。 搜索“until”，然后选择以下操作：“Until - 控制”

   ![添加“Until”循环](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. 通过选择 **Limit** 变量和**等于**运算符构建循环的退出条件。 
   输入 **10** 作为比较值。

   ![构建用于停止循环的退出条件](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. 在循环中，选择“添加操作”。 

1. 在搜索框下，选择“所有”。 搜索“变量”，然后选择以下操作：“递增变量 - 变量”

   ![添加用于递增变量的操作](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. 对于“名称”，选择 **Limit** 变量。 对于“值”，输入“1”。 

     ![按增幅 1 递增“Limit”](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. 在循环外部和循环下，选择“新建步骤”。 

1. 在搜索框下，选择“所有”。 
     查找并添加发送电子邮件的操作，例如： 

     ![添加用于发送电子邮件的操作](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. 根据提示登录到电子邮件帐户。

1. 设置电子邮件操作的属性。 将 **Limit** 变量添加到主题。 这样，你可以确认变量的当前值满足你指定的条件，例如：

      ![设置电子邮件属性](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | 属性 | 值 | 描述 |
      | -------- | ----- | ----------- | 
      | **目标** | *<email-address\@domain>* | 收件人的电子邮件地址。 若要进行测试，请使用你自己的电子邮件地址。 | 
      | **主题** | “限制”的当前值为 **Limit** | 指定电子邮件主题。 对于本例，请确保包括 **Limit** 变量。 | 
      | **Body** | <*email-content*> | 指定你要发送的电子邮件消息内容。 对于本例，输入你喜欢的任何文本。 | 
      |||| 

1. 保存逻辑应用。 若要手动测试逻辑应用，请在设计器工具栏上选择“运行”。

      在你的逻辑开始运行后，你将收到一封包含指定内容的电子邮件：

      ![收到的电子邮件](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>防止无限循环

Until 循环具有默认限制，用于在发生下列任一条件时停止执行：

| 属性 | 默认值 | 描述 | 
| -------- | ------------- | ----------- | 
| **Count** | 60 | 在循环退出之前运行的最大循环次数。 默认值为 60 个周期。 | 
| **超时** | PT1H | 在循环退出之前运行循环的最大时间量。 默认值为一小时，并且是以 ISO 8601 格式指定的。 <p>将针对每个循环周期评估超时值。 如果循环中的任何操作花费的时间超过超时限制，当前循环便不会停止。 但是，由于不满足限制条件，因此下一个循环不会启动。 | 
|||| 

若要更改这些默认限制，请在循环操作形状中选择“显示高级选项”。

<a name="until-json"></a>

## <a name="until-definition-json"></a>Until 定义 (JSON)

如果是在逻辑应用的代码视图中操作，可以改为在逻辑应用的 JSON 定义中定义 `Until` 循环，例如：

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

此示例“Until”循环调用可创建资源的 HTTP 终结点。 当 HTTP 响应主体返回 `Completed` 状态时，循环停止。 为防止无限循环，该循环在发生下列任一条件时也会停止：

* 循环运行了 10 次（由 `count` 属性指定）。 默认值为 60 次。 

* 循环已运行两小时（由 `timeout` 属性以 ISO 8601 格式指定）。 默认值为一小时。
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或者为其投票，请访问 [Azure 逻辑应用用户反馈站点](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于 （条件语句） 的条件运行步骤](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [基于不同的值 （switch 语句） 运行步骤](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行或合并并行步骤 （分支）](../logic-apps/logic-apps-control-flow-branches.md)
* [基于分组的操作状态 （作用域） 运行步骤](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
