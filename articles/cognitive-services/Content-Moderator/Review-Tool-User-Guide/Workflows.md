---
title: 定义和使用通过审阅工具-内容审查器内容工作流
titlesuffix: Azure Cognitive Services
description: Azure 内容审查器工作流设计器可用于定义自定义工作流和基于内容策略阈值。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795791"
---
# <a name="define-and-use-moderation-workflows"></a>定义和使用审查工作流

在本指南中，您将了解如何设置和使用[工作流](../review-api.md#workflows)上[审阅工具](https://contentmoderator.cognitive.microsoft.com)网站。 工作流是基于云的自定义筛选器可用来更有效地处理内容。 工作流可以连接到各种服务来以不同方式筛选内容，然后采取相应的措施。 本指南演示如何使用内容审查器连接器 （它包含默认情况下） 来筛选内容和设置在典型审查方案的人工审阅。

## <a name="create-a-new-workflow"></a>创建新的工作流

转到[内容审查器评审工具](https://contentmoderator.cognitive.microsoft.com/)并登录。 在“设置”选项卡中，选择“工作流”。

![“工作流”设置](images/2-workflows-0.png)

在下一个屏幕上，选择**添加工作流**。

![添加工作流](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>指定名称和说明

命名您的工作流，输入描述，然后选择是否在工作流将处理图像或文本。

![工作流名称和说明](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>定义评估条件

在下一个屏幕上转到**如果**部分。 在顶部的下拉列表菜单中，选择**条件**。 这将允许你配置工作流将在其执行操作的条件。 如果你想要使用多个条件，请选择**组合**相反。 

接下来，选择一个连接器。 本示例使用“内容审查器”。 具体取决于您选择的连接器，你将获得不同的数据输出选项。 请参阅[连接器](./configure.md#connectors)评审工具设置指南，了解如何设置其他连接器的部分。

![选择工作流连接器](images/image-workflow-connect-to.PNG)

选择所需的输出使用并将设置要检查与之相对的条件。

![定义工作流条件](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>定义操作

转到**然后**部分中，选择一项操作的位置。 以下示例创建图像审查，并将分配一个标记。 （可选） 你可以添加一种替代 (Else) 方案和也设置为该操作。

![定义工作流操作](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>保存工作流

记下的工作流名称;您需要使用工作流 API （见下文） 启动审查作业的名称。 最后，保存工作流使用**保存**在页面顶部的按钮。

## <a name="test-the-workflow"></a>测试工作流

现在，已经定义自定义工作流，它带有示例内容进行测试。 转到**工作流**，然后选择相应**执行工作流**按钮。

![工作流测试](images/image-workflow-execute.PNG)

将此文件保存[示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)到本地驱动器。 然后选择**选择文件**将映像上载到工作流。

![运行程序上叠加图像的报价单](images/sample-text.jpg)

### <a name="track-progress"></a>跟踪进度

可以在下一步的弹出窗口中查看工作流的进度。

![跟踪工作流的执行情况](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>验证工作流操作

转到**图像**选项卡上的**查看**并验证是否存在新创建的图像审查。

![审阅图像](images/image-workflow-review.PNG)

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何设置和使用内容审查器审查工作流[审阅工具](https://contentmoderator.cognitive.microsoft.com)。 接下来，请参阅[REST API 指南](../try-review-api-workflow.md)若要了解如何以编程方式创建工作流。
