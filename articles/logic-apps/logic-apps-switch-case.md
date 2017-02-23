---
title: "在 Azure 逻辑应用中使用 Switch 语句 | Microsoft Docs"
description: "通过 Switch 语句可轻松地根据逻辑应用中的表达式的值执行不同操作"
services: logic-apps
documentationcenter: dev-center-name
manager: erikre
ms.service: logic-apps
ms.devlang: wdl
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: deli
translationtype: Human Translation
ms.sourcegitcommit: 3a028507f9bbf15c8fd52ccc7c22a5763a9b1b3e
ms.openlocfilehash: 284cfca17b5abf785f7af9569c518c4400fe36fd


---
# <a name="use-switch-statement-in-logic-apps"></a>在逻辑应用中使用 Switch 语句
创作工作流时，通常需要根据对象或表达式的值执行不同操作。 例如，可能想让逻辑应用根据 HTTP 请求的状态代码或审批电子邮件的所选选项执行不同的操作。

可以通过使用 switch 语句实现这些方案：逻辑应用将评估令牌或对表达式进行求值，然后选择具有相同值的案例以在其中执行操作。 应只有一个案例与 Switch 语句相匹配。

 > [!TIP]
 > 与所有的编程语言相同，Switch 语句仅支持相等运算符。 如果需要其他的关系运算符（例如大于），请使用条件语句。
 >
 > 为了确保确定性的执行行为，案例必须包含唯一且静态的值，而不是动态令牌或表达式。

## <a name="prerequisites"></a>先决条件

- 有效的 Azure 订阅。
    - 如果没有有效的 Azure 订阅，请[创建免费帐户](https://azure.microsoft.com/free/)，或[免费试用逻辑应用](https://tryappservice.azure.com/)。
- [逻辑应用的基础知识](logic-apps-what-are-logic-apps.md)。

## <a name="working-with-switch-statement-in-designer"></a>在设计器中使用 Switch 语句
为了演示 Switch 语句的用法，我们将创建一个监视上传到 Dropbox 的文件的逻辑应用。 逻辑应用将发送一封审批电子邮件，以确定是否应将其传输到 SharePoint。 我们将使用 Switch 语句执行不同操作，具体取决于审批者所选的值。

1. 首先创建一个逻辑应用，然后选择“Dropbox - 当创建文件时”触发器。

 ![使用“Dropbox - 当创建文件时”触发器](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. 使用“Outlook.com - 发送审批电子邮件”操作跟进触发器。

 > [!TIP]
 > 逻辑应用还支持从 Office 365 Outlook 帐户发送审批电子邮件的方案。

 - 如果没有现有连接，系统会提示创建连接。
 - 填写必填字段，我们将向 approvers@contoso.com 发送电子邮件。
 - 在“用户选项”下，输入 `Approve, Reject`。

 ![配置连接](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. 添加 Switch 语句。
 - 选择“+ 新步骤”、**...“更多”**、“添加 Switch 语句”。
 - 我们想根据“发送审批电子邮件”操作的 `SelectedOptions` 输出选择要执行的操作，可在“添加动态内容”选择器中找到它。
 - 当用户选择了 `Approve` 时，使用“案例 1”进行处理。
    - 如果获得批准，请使用“SharePoint Online - 创建文件”操作将原始文件复制到 SharePoint Online 中。
    - 在案例中添加另一个操作，以通知用户 SharePoint 中有新文件可用。
 - 当用户选择了 `Reject` 时，请添加另一个案例进行处理。
    - 如果被拒绝，请发送通知电子邮件告知其他审批者该文件已被拒绝，无需任何进一步操作。
 - `SelectedOptions` 仅有两个可选选项，“默认”案例可以留空。

 ![Switch 语句](./media/logic-apps-switch-case/switch.jpg)

 > [!NOTE]
 > 除了默认案例外，Switch 语句至少还需要一个案例。

4. 在 Switch 语句后，请使用“Dropbox - 删除文件”操作删除上传到 Dropbox 的原始文件。

5. 保存逻辑应用，并通过上传文件到 Dropbox 对其进行测试。 应很快将收到一封审批电子邮件，请选择一个选项，然后观察行为。
 > [!TIP]
 > 查看如何[监视逻辑应用](logic-apps-monitor-your-logic-apps.md)。

## <a name="understanding-code-behind"></a>理解代码隐藏
现在已成功地使用 Switch 语句创建了逻辑应用。 让我们看看代码隐藏，如下所示。

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
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

`"Switch"` 是 Switch 语句的名称，可对其进行重命名以提高可读性。 `"type": "Switch"` 指示该操作是一个 Switch 语句。 在此案例中，将针对稍后将在定义中声明的每个案例，对用户选择的选项 `"expression"` 进行求值。 `"cases"` 可以包含任意数量的案例，如果没有与 Switch 表达式相匹配的案例，则将执行 `"default"` 内的操作。

`"cases"` 内可以包含任意数量的案例。 在本案例中，`"Case 1"` 是案例的名称，可对其进行重命名以提高可读性。 `"case"` 指定 Switch 表达式将与其进行比较的案例标签，它必须是常量和唯一值。  

## <a name="next-steps"></a>后续步骤
- 尝试使用其他的[逻辑应用功能](logic-apps-use-logic-app-features.md)。
- 了解[错误和异常处理](logic-apps-exception-handling.md)。
- 了解更多[工作流语言功能](logic-apps-author-definitions.md)。
- 如果有任何问题或反馈，请发表评论，或者[告诉我们如何改进逻辑应用](https://feedback.azure.com/forums/287593-logic-apps)。


<!--HONumber=Feb17_HO2-->


