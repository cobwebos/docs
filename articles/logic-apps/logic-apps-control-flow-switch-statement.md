---
title: 将 Switch 语句添加到工作流 - Azure 逻辑应用 | Microsoft Docs
description: 如何基于 Azure 逻辑应用中的特定值创建控制工作流操作的 Switch 语句
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e15f89d4b7e33ce7e28676c219344f7d7d9cd465
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299610"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>基于 Azure 逻辑应用中的特定值创建运行工作流操作的 Switch 语句

若要基于对象、表达式或令牌的值运行特定操作，请添加 Switch 语句。 此结构计算对象、表达式或令牌的值，选择与结果匹配的事例，并仅运行该事例的步骤。 switch 语句运行时，应只有一个 case 与结果相匹配。

例如，假设你想要一个根据电子邮件中所选的选项执行不同步骤的逻辑应用。 在此示例中，逻辑应用将检查网站的 RSS 源中是否有新内容。 当新项出现在 RSS 源中时，逻辑应用将向审批者发送电子邮件。 逻辑应用将根据审批者选择“批准”还是“拒绝”，执行不同的步骤。

> [!TIP]
> 与所有的编程语言相同，Switch 语句仅支持相等运算符。 如果需要其他关系运算符（例如“大于”），请使用[条件语句](#conditions)。
> 为了确保确定性的执行行为，case 必须包含唯一且静态的值，而不是动态令牌或表达式。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 若要执行本文中的示例，请使用 Outlook.com 或 Office 365 Outlook 帐户[创建此示例逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

  1. 添加用于发送电子邮件的操作时，请改为选择“发送审批电子邮件”。

     ![选择“发送审批电子邮件”](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  2. 提供必填字段，例如接收审批电子邮件的人员的电子邮件地址。 
  在“用户选项”下，输入“批准、拒绝”。

     ![输入电子邮件详细信息](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-a-switch-statement"></a>添加 Switch 语句

1. 在示例工作流的末尾，选择“+ 新建步骤” > **...更多”** > “添加 Switch 案例”。 

   ![添加 Switch 语句](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   此时将显示一个带有一个 case 和一个 default case 的 Switch 语句。 
   Switch 语句至少需要一个 case 以及 default case。 

   如果想要在步骤之间添加 switch 语句，请将指针移到要添加 switch 语句的箭头上。 
   选择出现的**加号** (**+**)，然后选择“添加 switch case”。

4. 在“基于”框中，选择 **SelectedOption** 字段，其输出确定要执行的操作。 
   
   可以从显示的“添加动态内容”列表中选择字段。

5. 若要处理审批者选择 `Approve` 或 `Reject`的情况，请在 **Case** 和 **Default** 之间添加另一个 case。 
   
6. 将这些操作添加到相应的 case：

   | Case # | **SelectedOption** | 操作 |
   |:------ |:-------------------|:------ |
   | Case 1 | **批准** | 添加 Outlook **发送电子邮件**操作，用于仅在审批者选择了“批准”时才发送有关该 RSS 项的详细信息。 |
   | Case 2 | **拒绝** | 添加 Outlook **发送电子邮件**操作，用于通知其他审批者该 RSS 项已被拒绝。 |
   | 默认 | \<无\> | 无需执行任何操作。 在此示例中，**Default** case 为空，因为 **SelectedOption** 只有两个选项。 |
   |         |          |

   ![Switch 语句](./media/logic-apps-control-flow-switch-statement/switch.png)

7. 保存逻辑应用。 

   若要手动测试此示例，请选择“运行”，直到逻辑应用找到新 RSS 项并发送审批电子邮件。 
   选择“批准”以观察结果。

## <a name="json-definition"></a>JSON 定义

现已使用 Switch 语句创建了逻辑应用，让我们来看一下 Switch 语句背后的高级代码定义。

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case" : {
         "actions" : {
           "Send_an_email": { }
         },
         "case" : "Approve"
      },
      "Case_2" : {
         "actions" : {
           "Send_an_email_2": { }
         },
         "case" : "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

| 标签              | 说明 |
| :----------------- | :---------- |
| `"Switch"`         | Switch 语句的名称，可对其进行重命名以提高可读性 |
| `"type": "Switch"` | 指示该操作是一个 Switch 语句 |
| `"expression"`     | 在此示例中，指定了审批者选择的选项，稍后将根据其对定义中声明的每个 case 进行求值 |
| `"cases"` | 可以定义任意数量的 case。 对于每个 case，`"Case_*"` 是该 case 的默认名称，可重命名以提高可读性 |
| `"case"` | 指定 case 的值，该值必须是 switch 语句用于比较的常量和唯一值。 如果没有 case 与 switch 表达式结果匹配，请将运行 `"default"` 部分中的操作。
|           |         |

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或为功能和建议投票，请访问 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于条件运行步骤（条件语句）](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [运行并重复执行步骤（循环）](../logic-apps/logic-apps-control-flow-loops.md)
* [运行或合并并行步骤（分支）](../logic-apps/logic-apps-control-flow-branches.md)
* [基于分组的操作状态运行步骤（作用域）](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
