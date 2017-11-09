---
title: "添加条件并启动工作流 - Azure 逻辑应用 | Microsoft Docs"
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
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-logic-apps-features"></a>使用逻辑应用功能

在[上一个主题](../logic-apps/logic-apps-create-a-logic-app.md)中已创建了第一个逻辑应用。 若要控制逻辑应用的工作流，可为逻辑应用指定不同的运行路径以及指定在数组、集合和批处理中处理数据的方式。 可在逻辑应用工作流中包括这些元素：

* 条件和[ 语句](../logic-apps/logic-apps-switch-case.md)可使逻辑应用根据是否符合特定条件来运行不同操作。

* [循环](../logic-apps/logic-apps-loops-and-scopes.md)可使逻辑应用重复运行步骤。 例如，使用 For_each 循环时可对数组重复执行操作。 如果使用 Until 循环，则可在符合条件时重复操作。

* 使用[范围](../logic-apps/logic-apps-loops-and-scopes.md)可将多个操作分到一组，例如用于实施异常处理等操作。

* 使用[解除批处理](../logic-apps/logic-apps-loops-and-scopes.md)可通过 SplitOn 命令，让逻辑应用为数组中的项启动单独的工作流。

本主题介绍生成逻辑应用的其他概念：

* 用于编辑现有逻辑应用的代码视图
* 用于启动工作流的选项

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>条件：仅在符合条件后运行步骤

为了使逻辑应用仅在数据符合特定条件时运行步骤，可添加一个将工作流中的数据与特定字段或值对比的条件。

例如，假设逻辑应用向你发送过多关于网站 RSS 源的文章的电子邮件。 则可以添加条件，使逻辑应用仅在新文章属于特定类别时发送电子邮件。

1. 在 [Azure 门户](https://portal.azure.com)中的逻辑应用设计器中查找并打开逻辑应用。

2. 将条件添加到所需的工作流位置。 

   若要在逻辑应用工作流的现有步骤之间添加条件，请将指针移到想添加条件的箭头上方。 
   选择“加号”(+”)，然后选择“添加条件”。 例如：

   ![向逻辑应用添加条件](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > 如果想在当前工作流末尾添加条件，请转到逻辑应用的底部，选择“+ 新建步骤”。

3. 立即定义条件。 指定想评估的源字段、要执行的操作以及目标值或字段。 若要将现有字段添加到条件，请从“添加动态内容列表”中进行选择。

   例如：

   ![在基本模式下编辑条件](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   下面是完整条件：

   ![完整条件](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > 若要在代码中定义条件，请选择“在高级模式下编辑”。 例如：
   > 
   > ![在代码中编辑条件](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. 在“IF YES”和“IF NO”下，根据是否符合条件添加要执行的步骤。

   例如：

   ![YES 和 NO 路径的条件](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > 可将现有操作拖动到“IF YES”和“IF NO”路径。

5. 完成后，保存逻辑应用。

现在将仅在文章符合条件时收到电子邮件。

## <a name="repeat-actions-over-a-list-with-foreach"></a>使用 forEach 对列表重复操作

forEach 循环可指定一个重复执行操作数组。 如果不是数组，则工作流会失败。 例如，如果有一个 action1，它输出了大量邮件，而你想发送每封邮件，则可以将此 forEach 语句包括在以下操作的属性中：`forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>编辑逻辑应用的代码定义

虽然具有逻辑应用设计器，不过可以直接编辑定义逻辑应用的代码。

1. 在命令栏中，选择“代码视图”。

    一个完整编辑器随即打开，并显示编辑的定义。

    ![代码视图](media/logic-apps-use-logic-app-features/codeview.png)

    在文本编辑器中，可以复制粘贴同一逻辑应用内或逻辑应用间任意数量的操作。 
    也可以从定义轻松添加或删除整节，还可以与其他人共享定义。

2. 若要保存编辑，请选择“保存”。

## <a name="parameters"></a>parameters

某些逻辑应用功能只能在代码视图中使用，例如参数。 使用参数可轻松在整个逻辑应用中重复使用值。 如果要在多个操作中使用某个电子邮件地址，应将该电子邮件地址定义为参数。

参数适用于将可能经常要更改的值分离出来。 在不同的环境下，如果要替换参数，这会特别有用。 若要了解如何根据环境替代参数，请参阅[创作逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)和 [REST API 文档](https://docs.microsoft.com/rest/api/logic)。

此示例演示如何更新现有逻辑应用，以便可以将参数用于查询词。

1. 在代码视图中找到 `parameters : {}` 对象，然后添加 `currentFeedUrl` 对象：

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. 转到 `When_a_feed-item_is_published` 操作，找到 `queries` 部分，然后将该查询值替换为：`"feedUrl": "#@{parameters('currentFeedUrl')}"` 

    若要联接两个或更多符串，还可以使用 `concat` 函数。 
    例如，`"@concat('#',parameters('currentFeedUrl'))"` 的工作方式与上面相同。

3.  完成后，选择“保存”。 

    现在，可通过将其他 URL 传递到 `currentFeedURL` 对象来更改网站的 RSS 源。

了解有关[如何创作逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)的详细信息。

## <a name="start-logic-app-workflows"></a>启动逻辑应用工作流

可使用不同选项启动逻辑应用中定义的工作流。 始终可以在 [Azure 门户]中按需启动工作流。

### <a name="recurrence-triggers"></a>定期触发器

定期触发器按指定的时间间隔运行。 当触发器具有条件逻辑时，该触发器绝确定是否需要运行工作流。 触发器通过返回 `200` 状态代码来指示应运行工作流。 当工作流无需运行时，触发器会返回 `202` 状态代码。

### <a name="callback-using-rest-apis"></a>使用 REST API 回调

若要启动工作流，服务可以调用逻辑应用终结点。 若要按需启动此类逻辑应用，请选择命令栏上的“立即运行”。 请参阅[通过将逻辑应用终结点作为触发器进行调用来启动工作流](../logic-apps/logic-apps-http-endpoint.md)。 

<!-- Shared links -->
[Azure 门户]: https://portal.azure.com

## <a name="next-steps"></a>后续步骤

* [Switch 语句](../logic-apps/logic-apps-switch-case.md) 
* [循环、范围和解除批处理](../logic-apps/logic-apps-loops-and-scopes.md)
* [编写逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)