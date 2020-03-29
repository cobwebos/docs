---
title: 通过审核工具使用内容审核 - 内容审阅者
titleSuffix: Azure Cognitive Services
description: 了解“审阅”工具如何允许人工审查者审阅 Web 门户中的图像。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: b02324923e3f004395105b8e04165390cb950fe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73044118"
---
# <a name="create-human-reviews"></a>创建人工评论

在本指南中，您将了解如何在"审阅"工具网站上设置[评论](../review-api.md#reviews)。 评论存储和显示内容，供人工版主评估。 审阅者可以更改应用的标记，并根据需要应用自己的自定义标记。 当用户完成审核时，结果将发送到指定的回调终结点，并且内容将从站点中删除。

## <a name="prerequisites"></a>先决条件

- 在内容审阅人[审核工具](https://contentmoderator.cognitive.microsoft.com/)网站上登录或创建帐户。

## <a name="image-reviews"></a>图像评审

1. 转到["审阅"工具](https://contentmoderator.cognitive.microsoft.com/)，选择 **"试用"** 选项卡，并上传一些图片进行审阅。
1. 上传的图像完成处理后，转到 **"审阅**"选项卡并选择 **"图像**"。

    ![显示审阅工具的 Chrome 浏览器，其中突出显示了“审阅图像”选项](images/review-images-1.png)

    图像显示与自动审核过程分配的任何标签。 您通过"审阅"工具提交的图像对其他审阅者不可见。

1. 或者，将 **"审核"移动到显示**滑块 （1） 以调整屏幕上显示的图像数。 单击**标记**或**未标记**的按钮 （2） 对图像进行排序。 单击标签面板 （3） 以将其打开或关闭。

    ![Chrome 浏览器显示带有标记图片的审阅工具以供审阅](images/review-images-2.png)

1. 要查看图像的详细信息，请单击缩略图中的省略号并选择 **"查看详细信息**"。 您可以使用 **"移动到"** 选项将图像分配给子团队（请参阅[团队](./configure.md#manage-team-and-subteams)部分以了解有关子团队详细信息）。

    ![突出显示“查看详细信息”选项的图像](images/review-images-3.png)

1. 浏览详细信息页面上的图像审阅信息。

    ![在单独窗格中列出审阅详细信息的图像](images/review-images-4.png)

1. 根据需要审核并更新标记分配后，单击****“下一步”即可提交评论。 提交后，你有大约五秒的时间来单击****“上一步”按钮返回上一屏幕并再次查看图像。 之后，图像不再位于“提交”队列中，并且****“上一步”按钮不再可用。

## <a name="text-reviews"></a>文本评审

文本审阅的功能类似于图像审阅。 您只需写入或粘贴文本（最多 1，024 个字符），即可不上传内容。 然后，内容审阅人分析文本并应用标记（除了其他审核信息，如亵渎和个人数据）。 在文本审阅中，您可以在提交审核之前切换应用的标记和/或应用自定义标记。

![评审工具的屏幕截图，显示 Chrome 浏览器窗口中的已标记文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何设置和使用内容审阅者[审阅工具](https://contentmoderator.cognitive.microsoft.com)的评论。 接下来，请参阅[REST API 指南](../try-review-api-review.md)或[.NET SDK 快速入门](../dotnet-sdk-quickstart.md)，了解如何以编程方式创建评论。