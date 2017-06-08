---
title: "添加条件逻辑并启动工作流 - Azure 逻辑应用 | Microsoft Docs"
description: "通过添加条件逻辑、触发器、操作和参数来控制工作流在 Azure 逻辑应用中的运行方式。"
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9f7d623ec213de6d46f59547aff9d4417ac95ede
ms.openlocfilehash: 41aafe94d24f0e22fe2256ab213c7668b670764c
ms.contentlocale: zh-cn
ms.lasthandoff: 02/15/2017


---
# <a name="use-logic-apps-features"></a>使用逻辑应用功能
在[上一个主题](../logic-apps/logic-apps-create-a-logic-app.md)中，创建了第一个逻辑应用。 现在你会使用 Azure 逻辑应用构建一个更完整的过程。 本主题介绍以下新的 Azure 逻辑应用概念：

* 条件逻辑，仅在满足特定条件时才执行操作。
* 代码视图，用于编辑现有逻辑应用。
* 用于启动工作流的选项。

应先完成[创建新的逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)中的步骤，再学习本主题。 在 [Azure 门户]中，浏览到逻辑应用，然后单击摘要中的“触发器和操作”编辑逻辑应用定义。

## <a name="reference-material"></a>参考资料
以下文档可能有用：

* [管理和运行时 REST API](https://msdn.microsoft.com/library/azure/mt643787.aspx) - 包括如何直接调用逻辑应用
* [语言参考](https://msdn.microsoft.com/library/azure/mt643789.aspx) - 所有受支持的函数/表达式的完整列表
* [触发器和操作类型](https://msdn.microsoft.com/library/azure/mt643939.aspx) - 它们采用的各种类型操作和输入值
* [应用服务概述](../app-service/app-service-value-prop-what-is.md) - 描述生成解决方案时需要选择的组件

## <a name="add-conditional-logic-to-your-logic-app"></a>将条件逻辑添加到逻辑应用

虽然逻辑应用的原始流可正常运行，不过我们可以改进一些领域。

### <a name="conditional"></a>条件逻辑

第一个逻辑应用可能会导致收到太多电子邮件。 以下步骤用于添加条件逻辑，以便仅当推文来自某个具有特定数量关注者的用户时，才会收到电子邮件。

0. 在逻辑应用设计器中，选择“新步骤”(+) >“添加操作”。
0.    查找并添加用于 Twitter 的“获取用户”操作。
0. 若要获取有关该 Twitter 用户的信息，请在触发器中查找并添加“推送人”字段。

    ![获取用户](media/logic-apps-use-logic-app-features/getuser.png)

0. 选择“新步骤”(+) >“添加条件”。
0. 若要对用户拥有的关注者数进行筛选，请在“对象名称”下，选择“添加动态内容”。 
0.    在搜索框中，查找并添加“关注者计数”字段。
0. 在“关系”，选择“大于”。
0. 在“值”中，输入希望用户拥有的关注者数。

    ![条件逻辑](media/logic-apps-use-logic-app-features/conditional.png)

0. 最后，将“发送电子邮件”框拖动到“If Yes”框。 

现在，仅当关注者计数满足条件时才会收到电子邮件。

## <a name="repeat-actions-over-a-list-with-foreach"></a>使用 forEach 对列表重复操作

forEach 循环可指定一个重复执行操作数组。 如果不是数组，则工作流将失败。 例如，如果有一个 action1，它输出了大量邮件，而你想发送每封邮件，则可以将此 forEach 语句包括在以下操作的属性中：`forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>编辑逻辑应用的代码定义

虽然具有逻辑应用设计器，不过可以直接编辑定义逻辑应用的代码。

1. 在命令栏中，选择“代码视图”。

    一个完整编辑器随即打开，并显示编辑的定义。

    ![代码视图](media/logic-apps-use-logic-app-features/codeview.png)

    在文本编辑器中，可以复制粘贴同一逻辑应用内或逻辑应用间任意数量的操作。 
    也可以从定义轻松添加或删除整节，还可以与其他人共享定义。

2. 若要保存编辑，请选择“保存”。

### <a name="parameters"></a>parameters

某些逻辑应用功能只能在代码视图中使用，例如参数。 使用参数可轻松在整个逻辑应用中重复使用值。 如果要在多个操作中使用某个电子邮件地址，应将该电子邮件地址定义为参数。

参数适用于将可能经常要更改的值分离出来。 在不同的环境下，如果要替换参数，这会特别有用。 若要了解如何根据环境替代参数，请参阅 [REST API 文档](https://docs.microsoft.com/rest/api/logic)。

此示例演示如何更新现有逻辑应用，以便可以将参数用于查询词。

1. 在代码视图中找到 `parameters : {}` 对象，并添加一个主题对象：

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }

2. 转到 `twitterconnector` 操作，找到查询值，然后将该值替换为 `#@{parameters('topic')}`。 

    若要联接两个或更多符串，还可以使用 `concat` 函数。 
    例如，`@concat('#',parameters('topic'))` 的工作方式与上面相同。

3.    完成后，选择“保存”。 

    现在每个小时都会收到新的推文，其中有 5 篇以上的转推会传递到 Dropbox 中名为 **tweets** 的文件夹。

若要了解有关逻辑应用定义的详细信息，请参阅[创作逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)。

## <a name="start-logic-app-workflows"></a>启动逻辑应用工作流

可使用不同选项启动逻辑应用中定义的工作流。 始终可以在 [Azure 门户]中按需启动工作流。

### <a name="recurrence-triggers"></a>定期触发器

定期触发器按指定的时间间隔运行。 当触发器具有条件逻辑时，该触发器绝确定是否需要运行工作流。 触发器通过返回 `200` 状态代码来指示应运行工作流。 当工作流无需运行时，触发器会返回 `202` 状态代码。

### <a name="callback-using-rest-apis"></a>使用 REST API 回调

若要启动工作流，服务可以调用逻辑应用终结点。 若要按需启动此类逻辑应用，请选择命令栏上的“立即运行”。 请参阅[通过将逻辑应用终结点作为触发器进行调用来启动工作流](../logic-apps/logic-apps-http-endpoint.md)。 

<!-- Shared links -->
[Azure 门户]: https://portal.azure.com

