---
title: 添加循环重复执行操作或处理数组 - Azure 逻辑应用 | Microsoft Docs
description: 如何在 Azure 逻辑应用中创建重复执行工作流操作或处理数组的循环
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 87595eeb0330a2d8210258c097c29b205b628cf4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298179"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>在 Azure 逻辑应用中添加循环以重复执行操作或处理数组

若要在逻辑应用中循环访问数组，可以使用 [Foreach 循环](#foreach-loop)或[顺序的 Foreach 循环](#sequential-foreach-loop)。 标准“Foreach”循环中的迭代并行运行，而顺序的“Foreach”循环中的迭代一次运行一个。 若要了解 Foreach 循环在单个逻辑应用运行中可以处理的最大数组项数，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

> [!TIP] 
> 如果你有接收数组的触发器并且希望针对每个数组项运行工作流，则可以使用 [**SplitOn** 触发器属性](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)“分离”该数组。 
  
若要重复执行操作，直到满足某个条件或某个状态发生更改，请使用 [Until 循环](#until-loop)。 逻辑应用首先执行循环内的所有操作，然后在最后的步骤中检查条件。 如果满足该条件，则循环将停止。 否则，循环将继续进行。 若要了解单个逻辑应用运行中 Until 循环的最大数目，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。 

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Foreach 循环

若要针对数组中的每个项重复执行操作，请在逻辑应用工作流中使用 Foreach 循环。 可以在单个 Foreach 循环中包括多个操作，并且可以将 Foreach 循环互相嵌套。 默认情况下，标准 Foreach 循环中的周期并行运行。 若要了解每个 Foreach 循环可以运行的最大并行周期数，请参阅[限制和配置](../logic-apps/logic-apps-limits-and-config.md)。

> [!NOTE] 
> 一个 Foreach 循环仅处理一个数组，并且循环中的操作使用 `@item()` 引用来处理数组中的每个项。 如果指定了不在数组中的数据，则逻辑应用工作流将失败。 

例如，以下逻辑应用发送来自网站的 RSS 源的每日摘要。 该应用使用 Foreach 循环针对找到的每个新项发送一封电子邮件。

1. 使用 Outlook.com 或 Office 365 Outlook 帐户[创建此示例逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

2. 在 RSS 触发器与“发送电子邮件”操作之间，添加一个 Foreach 循环。 

   若要在步骤之间添加循环，请将指针移动到要添加循环的箭头之上。 
   选择出现的**加号** (**+**)，然后选择“添加 for each”。

   ![在步骤之间添加 Foreach 循环](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. 现在，构建循环。 在“添加动态内容”列表出现后，在“选择来自之前步骤的输出”下，选择“源链接”数组，这是来自 RSS 触发器的输出。 

   ![从动态内容列表进行选择](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 只能选择来自之前步骤的数组输出。

   所选数组现在显示在此处：

   ![选择数组](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 若要对每个数组项执行操作，请将“发送电子邮件”操作拖到 **For each** 循环中。 

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
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Foreach 循环：顺序的

默认情况下，Foreach 循环中的每个周期针对每个数组项并行运行。 若要按顺序运行每个周期，请在 Foreach 循环中设置“顺序的”选项。

1. 在循环的右上角，选择**省略号** (**...**) >“设置”。

   ![在 Foreach 循环中，选择“...”>“设置”](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. 开启“顺序的”设置，然后选择“完成”。

   ![开启 Foreach 循环的“顺序的”设置](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

还可以在逻辑应用的 JSON 定义中将 **operationOptions** 参数设置为 `Sequential`。 例如：

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Until 循环
  
若要重复执行操作，直到满足某个条件或某个状态发生更改，请在逻辑应用工作流中使用 Until 循环。 下面是可以在其中使用 Until 循环的一些常见用例：

* 调用某个终结点，直至获得想要的响应。
* 在数据库中创建一条记录，等到该记录中的特定字段得到批准，然后继续进行处理。 

例如，在每天的上午 8:00，此逻辑应用都递增某个变量的值，直到该变量的值等于 10。 然后，该逻辑应用将发送一封电子邮件来确认当前值。 虽然此示例使用的是 Office 365 Outlook，但是也可以使用逻辑应用支持的任何电子邮件提供程序（[查看此处的连接器列表](https://docs.microsoft.com/connectors/)）。 如果使用其他电子邮件帐户，整个步骤仍然是相同的，但 UI 可能稍有不同。 

1. 创建空白逻辑应用。 在逻辑应用设计器中，搜索“定期”，然后选择以下触发器：“计划 - 定期”。 

   ![添加“计划 - 定期”触发器](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. 通过设置时间间隔、频率和天中的小时来指定触发器何时触发。 若要设置小时，请选择“显示高级选项”。

   ![添加“计划 - 定期”触发器](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | 属性 | 值 |
   | -------- | ----- |
   | **间隔** | 1 | 
   | **频率** | 日期 |
   | **在这些小时** | 8 |
   ||| 

3. 在该触发器下，选择“新建步骤” > “添加操作”。 搜索“变量”，然后选择以下操作：“变量 - 初始化变量”

   ![添加“变量 - 初始化变量”操作](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. 使用以下值设置变量：

   ![设置变量属性](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | 属性 | 值 | 说明 |
   | -------- | ----- | ----------- |
   | **名称** | Limit | 变量的名称 | 
   | **类型** | Integer | 变量的数据类型 | 
   | **值** | 0 | 变量的起始值 | 
   |||| 

5. 在“初始化变量”操作下，选择“新建步骤” > “更多”。 选择以下循环：“添加 do until”

   ![添加 do until 循环](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. 通过选择 **Limit** 变量和**等于**运算符构建循环的退出条件。 输入 **10** 作为比较值。

   ![构建用于停止循环的退出条件](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. 在循环中，选择“添加操作”。 搜索“变量”，然后添加以下操作：“变量 - 递增变量”

   ![添加用于递增变量的操作](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. 对于“名称”，选择 **Limit** 变量。 对于“值”，输入“1”。 

   ![按增幅 1 递增“Limit”](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. 在循环下面但在其外部，添加一个用于发送电子邮件的操作。 根据提示登录到电子邮件帐户。

   ![添加用于发送电子邮件的操作](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. 设置电子邮件的属性。 将 **Limit** 变量添加到主题。 这样，你可以确认变量的当前值满足你指定的条件，例如：

    ![设置电子邮件属性](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | 属性 | 值 | 说明 |
    | -------- | ----- | ----------- | 
    | **收件人** | *<email-address@domain>* | 收件人的电子邮件地址。 若要进行测试，请使用你自己的电子邮件地址。 | 
    | **主题** | “限制”的当前值为 **Limit** | 指定电子邮件主题。 对于本例，请确保包括 **Limit** 变量。 | 
    | **正文** | <*email-content*> | 指定你要发送的电子邮件消息内容。 对于本例，输入你喜欢的任何文本。 | 
    |||| 

11. 保存逻辑应用。 若要手动测试逻辑应用，请在设计器工具栏上选择“运行”。

    在你的逻辑开始运行后，你将收到一封包含指定内容的电子邮件：

    ![收到的电子邮件](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>防止无限循环

Until 循环具有默认限制，用于在发生下列任一条件时停止执行：

| 属性 | 默认值 | 说明 | 
| -------- | ------------- | ----------- | 
| **Count** | 60 | 在循环退出之前运行的最大循环次数。 默认值为 60 个周期。 | 
| **超时** | PT1H | 在循环退出之前运行循环的最大时间量。 默认值为一小时，并且是以 ISO 8601 格式指定的。 <p>将针对每个循环周期评估超时值。 如果循环中有任何操作花费的时间多于超时限制，则当前周期不会停止，但下一周期不会启动，因为不满足限制条件。 | 
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
        },
    }
},
```

在另一个示例中，此 Until 循环调用一个 HTTP 终结点，该终结点在 HTTP 响应正文以“已完成”状态返回时创建一个资源并停止。 为防止无限循环，该循环在发生下列任一条件时也会停止：

* 循环已运行了 10 次（由 `count` 属性指定）。 默认值为 60 次。 
* 循环尝试运行的时间已达两小时（由 `timeout` 属性以 ISO 8601 格式指定）。 默认值为一小时。
  
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
},
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或者为其投票，请访问 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于条件运行步骤（条件语句）](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [基于不同的值运行步骤（switch 语句）](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行或合并并行步骤（分支）](../logic-apps/logic-apps-control-flow-branches.md)
* [基于分组的操作状态运行步骤（作用域）](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
