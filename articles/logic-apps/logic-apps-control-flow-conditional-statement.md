---
title: 将条件语句添加到工作流 - Azure 逻辑应用 | Microsoft Docs
description: 如何在 Azure 逻辑应用中创建控制工作流操作的条件
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e8d84944d44588602593c762c4f60c375e480343
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298162"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>在 Azure 逻辑应用中创建控制工作流操作的条件语句

若要仅在通过指定条件后在逻辑应用程序中运行指定操作，请添加条件语句。 此结构将工作流中的数据与特定值或字段进行比较。 然后，可以定义基于数据是否符合条件运行的不同操作。 可以将一个条件嵌套在另一个条件中。

例如，假设你有一个逻辑应用，当网站的 RSS 源中出现新项时，该应用发送的电子邮件过多。 你可以添加一个条件语句，以便仅在新项包含特定字符串时才发送电子邮件。 

> [!TIP]
> 若要基于不同的特定值运行不同步骤，请改用 [*switch 语句*](../logic-apps/logic-apps-control-flow-switch-statement.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 若要执行本文中的示例，请使用 Outlook.com 或 Office 365 Outlook 帐户[创建此示例逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="add-a-condition"></a>添加条件

1. 在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的逻辑应用设计器中打开逻辑应用。

2. 在所需的位置添加条件。 

   若要在步骤之间添加条件，请将指针移到要添加条件的箭头上。 选择出现的**加号** (**+**)，然后选择“添加条件”。 例如：

   ![在步骤之间添加条件](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   如果想要在工作流末尾添加条件，请在逻辑应用的底部选择“+ 新建步骤”>“添加条件”。

3. 在“条件”下，创建条件。 

   1. 在左侧框中，指定要比较的数据或字段。

      从“添加动态内容”列表中，可以选择现有逻辑应用中的字段。

   2. 在中间的列表中，选择要执行的操作。 
   3. 在右侧框中，指定作为条件的值或字段。

   例如：

   ![在基本模式下编辑条件](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   下面是完整条件：

   ![完整条件](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > 若要创建更高级的条件或使用表达式，请选择“在高级模式下编辑”。 可以使用[工作流定义语言](../logic-apps/logic-apps-workflow-definition-language.md)定义的表达式。
   > 
   > 例如：
   >
   > ![在代码中编辑条件](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. 在“IF YES”和“IF NO”下，根据是否符合条件添加要执行的步骤。 例如：

   ![YES 和 NO 路径的条件](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > 可将现有操作拖动到“IF YES”和“IF NO”路径。

6. 保存逻辑应用。

现在此逻辑应用仅当 RSS 源中的新项符合条件时才发送邮件。

## <a name="json-definition"></a>JSON 定义

现已使用条件语句创建了逻辑应用，让我们来看一下条件语句背后的高级代码定义。

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能和建议或者为其投票，请访问 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* [基于不同的值运行步骤（switch 语句）](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [运行并重复执行步骤（循环）](../logic-apps/logic-apps-control-flow-loops.md)
* [运行或合并并行步骤（分支）](../logic-apps/logic-apps-control-flow-branches.md)
* [基于分组的操作状态运行步骤（作用域）](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
