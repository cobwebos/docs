---
title: 通过"审阅"工具定义和使用内容工作流 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 可以使用 Azure 内容审阅者工作流设计器根据内容策略定义自定义工作流和阈值。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754252"
---
# <a name="define-and-use-moderation-workflows"></a>定义和使用审核工作流

在本指南中，您将了解如何在["审阅"工具](https://contentmoderator.cognitive.microsoft.com)网站上设置和使用[工作流](../review-api.md#workflows)。 工作流是基于云的自定义筛选器，可用于更高效地处理内容。 工作流可以连接到各种服务，以不同的方式筛选内容，然后采取适当的操作。 本指南演示如何使用内容审阅者连接器（默认情况下包含）在典型的审核方案中筛选内容和设置人工评论。

## <a name="create-a-new-workflow"></a>创建新工作流

转到[内容审阅者审阅工具](https://contentmoderator.cognitive.microsoft.com/)并登录。 在“设置”**** 选项卡中，选择“工作流”****。

![“工作流”设置](images/2-workflows-0.png)

在下一个屏幕上，选择 **"添加工作流**"。

![添加工作流](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指定名称和说明

命名工作流，输入说明，并选择工作流是处理图像还是文本。

![工作流名称和说明](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定义评估标准

在下一个屏幕上，转到 **"If"** 部分。 在顶部下拉菜单中，选择 **"条件**"。 这将允许您配置工作流将执行操作的条件。 如果要使用多个条件，请选择 **"组合**"。 

接下来，选择连接器。 本示例使用“内容审查器”****。 根据您选择的连接器，您将获得不同的数据输出选项。 请参阅"查看"工具设置指南中的["连接器](./configure.md#connectors)"部分，了解如何设置其他连接器。

![选择工作流连接器](images/image-workflow-connect-to.PNG)

选择要使用的输出，并设置要检查的条件。

![定义工作流条件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定义操作

转到"**然后"** 部分，在其中选择操作。 下面的示例创建图像审阅并分配标记。 或者，您可以添加备用 （Else） 路径并为其设置操作。

![定义工作流操作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>保存工作流

请注意工作流名称;您需要名称才能使用工作流 API 启动审核作业（见下文）。 最后，使用页面顶部的 **"保存**"按钮保存工作流。

## <a name="test-the-workflow"></a>测试工作流

现在，您已经定义了自定义工作流，使用示例内容对其进行测试。 转到**工作流**并选择相应的**执行工作流**按钮。

![工作流测试](images/image-workflow-execute.PNG)

将[此示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)保存到本地驱动器。 然后**选择"选择文件"** 并将图像上载到工作流。

![在图像上叠加了报价的跑步者](images/sample-text.jpg)

### <a name="track-progress"></a>跟踪进度

您可以在下一个弹出窗口中查看工作流的进度。

![跟踪工作流的执行情况](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>验证工作流操作

转到 **"审阅**"下的 **"图像**"选项卡，并验证是否有新创建的映像审阅。

![审阅图像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>后续步骤

在本指南中，您从内容审阅人[审阅工具](https://contentmoderator.cognitive.microsoft.com)中学习了如何设置和使用审核工作流。 接下来，请参阅[REST API 指南](../try-review-api-workflow.md)，了解如何以编程方式创建工作流。
