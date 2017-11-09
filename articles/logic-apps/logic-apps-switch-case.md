---
title: "Azure 逻辑应用中适用于不同操作的 Switch 语句 | Microsoft Docs"
description: "通过使用 Switch 语句，根据表达式值选择要在逻辑应用中执行的不同操作"
services: logic-apps
keywords: "Switch 语句"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>使用 Switch 语句在逻辑应用中执行不同操作

创作工作流时，通常需要根据对象或表达式的值执行不同操作。 例如，可能想让逻辑应用根据 HTTP 请求的状态代码或电子邮件中的所选选项执行不同的操作。

可使用 Switch 语句实现这些方案。 逻辑应用可以评估令牌或表达式，并选择具有相同值的案例来执行指定操作。 应只有一个案例与 Switch 语句相匹配。

> [!TIP]
> 与所有的编程语言相同，Switch 语句仅支持相等运算符。 如果需要其他关系运算符（例如大于），请使用条件语句。
> 为了确保确定性的执行行为，案例必须包含唯一且静态的值，而不是动态令牌或表达式。

## <a name="prerequisites"></a>先决条件

- 一个有效的 Azure 订阅。 如果没有有效的 Azure 订阅，请[创建免费帐户](https://azure.microsoft.com/free/)，或[免费试用逻辑应用](https://tryappservice.azure.com/)。
- [有关逻辑应用的基本知识](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>将 Switch 语句添加到工作流

为了演示 Switch 语句的工作原理，此示例将创建一个监视上传到 Dropbox 的文件的逻辑应用。 当上传新文件时，逻辑应用会向选择是否将这些文件传输到 SharePoint 的审批人发送电子邮件。 该应用使用根据审批人选择的值执行不同操作的 switch 语句。

1. 创建一个逻辑应用，并选择此触发器：“Dropbox - 创建文件时”。

   ![使用“Dropbox - 当创建文件时”触发器](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. 在该触发器下，添加此操作：“Outlook.com - 发送审批电子邮件”

   > [!TIP]
   > 逻辑应用还支持从 Office 365 Outlook 帐户发送审批电子邮件的方案。

   - 如果没有现有连接，系统会提示创建连接。
   - 填写必填字段。 例如，在“收件人”下，指定用于发送审批人电子邮件的电子邮件地址。
   - 在“用户选项”下，输入 `Approve, Reject`。

   ![配置连接](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. 添加 Switch 语句。

   - 选择“+ 新步骤” > “...更多”** > “添加 Switch 案例”。 
   - 现在，我们要根据“发送审批电子邮件”操作的 `SelectedOptions` 输出来选择要执行的操作。 
   可在“添加动态内容”选择器中找到此字段。
   - 当审批者选择了 `Approve` 时，使用“案例 1”进行处理。
     - 如果获得批准，请使用[“SharePoint Online - 创建文件”操作](../connectors/connectors-create-api-sharepointonline.md)将原始文件复制到 SharePoint Online 中。
     - 在案例中添加另一个操作，以通知用户 SharePoint 中有新文件可用。
   - 当用户选择了 `Reject` 时，请添加另一个案例进行处理。
     - 如果被拒绝，请发送通知电子邮件告知其他审批者该文件已被拒绝，无需任何进一步操作。
   - `SelectedOptions` 只提供两个选项，因此可以将“默认”案例保留为空。

   ![Switch 语句](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > 除了默认案例外，Switch 语句至少还需要一个案例。

4. 在 Switch 语句后，请通过添加此操作删除上传到 Dropbox 的原始文件：“Dropbox - 删除文件”

5. 保存逻辑应用。 通过将文件上传到 Dropbox 来测试应用。 应会很快收到审批电子邮件。 选择一个选项，并观察行为。

   > [!TIP]
   > 查看如何[监视逻辑应用](logic-apps-monitor-your-logic-apps.md)。

## <a name="understand-the-code-behind-switch-statements"></a>了解 Switch 语句后面的代码

已使用 Switch 语句成功创建逻辑应用，现在来看看 Switch 语句后面的代码定义。

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

* `"Switch"` 是 Switch 语句的名称，可对其进行重命名以提高可读性。 
* `"type": "Switch"` 指示该操作是一个 Switch 语句。 
* 在此示例中，`"expression"` 是审批人选择的选项，将针对稍后会在定义中声明的每个案例对其进行求值。 
* `"cases"` 可以包含任意数量的案例。 对于每个案例，`"Case *"` 是案例的默认名称，可重命名以提高可读性。 
`"case"` 指定 Switch 表达式用于比较的案例标签，它必须是常量和唯一值。 如果没有任何案例与 Switch 表达式相匹配，则执行 `"default"` 下的操作。

## <a name="get-help"></a>获取帮助

若要提问、解答问题和了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

- 了解如何[添加条件](logic-apps-use-logic-app-features.md)
- 了解[错误和异常处理](logic-apps-exception-handling.md)
- 了解更多[工作流语言功能](logic-apps-author-definitions.md)