---
title: 通过审阅工具定义和使用内容工作流 - 内容审查器
titleSuffix: Azure Cognitive Services
description: 可使用 Azure 内容审查器工作流设计器，根据内容策略来定义自定义工作流和阈值。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: pafarley
ms.openlocfilehash: 0be77dc1ac249c37f9b59078451d8fbe35f17458
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "72754252"
---
# <a name="define-and-use-moderation-workflows"></a>定义和使用审查工作流

本指南将介绍如何在[审阅工具](https://contentmoderator.cognitive.microsoft.com)网站上设置和使用[工作流](../review-api.md#workflows)。 工作流是基于云的自定义筛选器，可用于更有效地处理内容。 工作流可以连接到多种服务，以通过不同的方式筛选内容，然后采取相应操作。 本指南演示如何在典型审查方案中使用内容审查器连接器（默认包含）来筛选内容并设置人工评审。

## <a name="create-a-new-workflow"></a>创建新工作流

转到[内容审查器审阅工具](https://contentmoderator.cognitive.microsoft.com/)并登录。 在“设置”**** 选项卡中，选择“工作流”****。

![“工作流”设置](images/2-workflows-0.png)

在下一个屏幕上选择“添加工作流”****。

![添加工作流](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指定名称和说明

命名工作流，输入说明，并选择工作流处理的是图像还是文本。

![工作流名称和说明](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定义评估标准

在下一个屏幕上，转到“If”部分****。 在顶部的下拉菜单中，选择“条件”****。 可通过此选项配置工作流将采取操作所依据的条件。 如果要使用多个条件，请改为选择“组合”****。 

接下来，选择一个连接器。 本示例使用“内容审查器”****。 根据所选的连接器，你将获得不同的数据输出选项。 请参阅“审阅工具”设置指南的[连接器](./configure.md#connectors)部分，了解如何设置其他连接器。

![选择工作流连接器](images/image-workflow-connect-to.PNG)

选择要使用的所需输出，并设置对其进行检查的条件。

![定义工作流条件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定义操作

转到“Then”部分，在其中选择操作****。 下面的示例创建图像审阅并分配标记。 此外，还可以添加替代（其他）路径，并同样为其设置操作。

![定义工作流操作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>保存工作流

记下工作流名称；需要使用该名称通过工作流 API 来启动审查作业（见下文）。 最后，使用页面顶部的“保存”按钮来保存工作流****。

## <a name="test-the-workflow"></a>测试工作流

定义自定义工作流之后，接下来即可使用示例内容对其进行测试。 转到“工作流”，选择相应的“执行工作流”按钮**** ****。

![工作流测试](images/image-workflow-execute.PNG)

将此[示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)保存到本地驱动器。 然后选择“选择一个或多个文件”****，并将图像上传到工作流。

![跑步者图像并叠加有引文](images/sample-text.jpg)

### <a name="track-progress"></a>跟踪进度

可在下一个弹出窗口中查看工作流的进度。

![跟踪工作流的执行情况](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>验证工作流操作

转到“审阅”下的“图像”选项卡，然后验证是否存在新创建的图像审阅**** ****。

![审阅图像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>后续步骤

本指南介绍了如何从内容审查器[审阅工具](https://contentmoderator.cognitive.microsoft.com)设置和使用审查工作流。 接下来，请参阅 [REST API 指南](../try-review-api-workflow.md)，了解如何以编程方式创建工作流。
