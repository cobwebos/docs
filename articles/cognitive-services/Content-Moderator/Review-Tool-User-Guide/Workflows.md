---
title: 在 Azure 内容审查器中定义和使用工作流 | Microsoft Docs
description: 了解如何根据内容策略来创建自定义工作流。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365466"
---
# <a name="define-test-and-use-workflows"></a>定义、测试和使用工作流

使用 Azure 内容审查器工作流设计器和 API，可以根据内容策略来定义自定义工作流和阈值。

工作流通过连接器“连接”到内容审查器 API。 可使用其他 API，前提是有此 API 对应的连接器。 本文中的示例使用默认包含的内容审查器连接器。

## <a name="browse-to-the-workflows-section"></a>转到“工作流”部分

在“设置”选项卡中，选择“工作流”。

  ![“工作流”设置](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>启动新工作流

选择“添加工作流”。

  ![添加工作流](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>指定名称和说明

命名工作流，输入说明，并选择工作流处理的是图像还是文本。

  ![工作流名称和说明](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>定义评估标准（“条件”）

下面的屏幕截图展示了需要为工作流定义的字段和 If-Then-Else 选择。 选择连接器。 本示例使用“内容审查器”。 可用的输出更改选项具体视选择的连接器而定。

  ![定义工作流条件](images/ocr-workflow-step-2-condition.PNG)

选择所需的连接器及其输出后，选择运算符和条件值。

## <a name="define-the-action-to-take"></a>定义要执行的操作

选择要执行的操作和要满足的条件。 下面的示例展示了如何创建图像审阅，分配标记 `a`，并突出显示满足所示条件的图像。 还可以结合使用多个条件来获取所需结果。 根据需要，添加备用 (Else) 路径。

  ![定义工作流操作](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>保存工作流

最后，保存工作流，并记录工作流名称。 必须有名称，才能使用审阅 API 启动审查。

## <a name="test-the-workflow"></a>测试工作流

现已定义自定义工作流，是时候使用示例内容测试它了。

选择相应的“执行工作流”按钮。

  ![工作流测试](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>上传文件

将[示例图像](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)保存到本地驱动器。 若要测试工作流，请选择“选择一个或多个文件”，并上传图像。

  ![上传图像文件](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>跟踪工作流

跟踪工作流的执行情况。

  ![跟踪工作流的执行情况](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>审阅已标记为人工审查的任何图像

若要执行图像审阅，请转到“审阅”下的“图像”选项卡。

  ![审阅图像](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>后续步骤 

若要通过代码调用工作流，请在使用自定义工作流时，参阅 [`Job`API 控制台快速入门](../try-review-api-job.md)和 [.NET SDK 快速入门](../moderation-jobs-quickstart-dotnet.md)。
