---
title: "添加用于运行工作流的条件和触发器 - Azure 逻辑应用 | Microsoft 文档"
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
ms.author: stepsic
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: 330359583346f7e1944b53ec3fd34104f10e1261


---
# <a name="use-logic-apps-features"></a>使用逻辑应用功能
在[上一个主题](../logic-apps/logic-apps-create-a-logic-app.md)中，创建了第一个逻辑应用。 现在，我们会演示如何使用应用服务逻辑应用生成更完整的过程。 本主题介绍了以下新的逻辑应用概念：

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

## <a name="adding-conditional-logic"></a>添加条件逻辑
尽管原始流有效，但有些地方可以改进。

### <a name="conditional"></a>条件性
此逻辑应用可能导致收到大量电子邮件。 以下步骤用于添加逻辑，确保当推文来自某个具有一定数量关注者的用户时，只会收到一封电子邮件。

1. 单击加号并找到 Twitter 的“获取用户”操作。
2. 在触发器中的“推送人”字段传递，以获取有关该 Twitter 用户的信息。

    ![获取用户](media/logic-apps-use-logic-app-features/getuser.png)
3. 再次单击加号，但这次选择“添加条件”
4. 在第一个对话框中的“获取用户”下单击“...”找到“关注者计数”字段。
5. 在下拉列表中，选择“大于”
6. 在第二个对话框中，键入想要的关注者数量。

    ![条件性](media/logic-apps-use-logic-app-features/conditional.png)
7. 最后，将电子邮件拖放到“If Yes”对话框。 这意味着，只有在满足关注着数量时才会收到电子邮件。

## <a name="repeating-over-a-list-with-foreach"></a>使用 forEach 对列表重复操作
ForEach 循环指定一组数列，重复操作。 如果不是数组，则工作流将失败。 举个例子，如果有一个 action1，它输出了大量邮件，而你想发送每封邮件，则可以将此 forEach 语句包括在以下操作的属性中：forEach : "@action('action1').outputs.messages"

## <a name="using-the-code-view-to-edit-a-logic-app"></a>使用代码视图编辑逻辑应用
如下所示，除了设计器，还可以直接编辑定义逻辑应用的代码。

1. 单击命令栏中的“代码视图”按钮。

    这将打开一个完整的编辑器，并显示有刚编辑的定义。

    ![代码视图](media/logic-apps-use-logic-app-features/codeview.png)

    通过使用文本编辑器，可以复制粘贴同一逻辑应用内或逻辑应用间任意数量的操作。 也可以从定义轻松添加或删除整节，还可以与其他人共享定义。
2. 在代码视图中进行更改后，只需单击“保存”。

### <a name="parameters"></a>parameters
逻辑应用有些功能只能在代码视图中使用。 例如参数。 使用参数可轻松在整个逻辑应用中重复使用值。 如果想要在多个操作中使用某个电子邮件地址，应将其定义为参数。

以下参数将更新现有的逻辑应用，以便将参数用于查询词。

1. 在“代码视图”中，找到 `parameters : {}` 对象并插入以下主题对象：

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
2. 滚动到 `twitterconnector` 操作，找到查询值，然后将其替换为 `#@{parameters('topic')}`。
    还可以使用 **concat** 函数连接两个或多个字符串，例如：`@concat('#',parameters('topic'))` 与以上参数完全相同。

使用参数是提取可能发生重大更改的值的好办法。 当需要在各种环境中替代参数时，它们就特别有用。 有关如何根据环境替代参数的详细信息，请参阅 [REST API 文档](https://msdn.microsoft.com/library/mt643787.aspx)。

现在，单击“保存”时，每个小时都会收到新的推文，其中有 5 篇以上的转推会传递到 Dropbox 中叫做 **tweets** 的文件夹。

若要了解有关逻辑应用定义的详细信息，请参阅[创作逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)。

## <a name="starting-a-logic-app-workflow"></a>启动逻辑应用工作流
启动逻辑应用中定义的工作流具有多种不同的选项。 始终可以在 [Azure 门户]中按需启动工作流。

### <a name="recurrence-triggers"></a>定期触发器
定期触发器按指定的时间间隔运行。 当触发器具有条件逻辑时，该触发器将确定是否需要运行工作流。 触发器通过返回 `200` 状态代码指示它应运行。 当它不需要运行时，它将返回 `202` 状态代码。

### <a name="callback-using-rest-apis"></a>使用 REST API 回调
服务可以调用逻辑应用终结点，以启动工作流。 有关详细信息，请参阅[作为可调用终结点的逻辑应用](../logic-apps/logic-apps-http-endpoint.md)。 若要按需启动该类逻辑应用，请单击命令栏上的“立即运行”按钮。 

<!-- Shared links -->
[Azure 门户]: https://portal.azure.com



<!--HONumber=Jan17_HO4-->


