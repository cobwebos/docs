---
title: 使用内容通过审阅工具-内容审查器评审
titlesuffix: Azure Cognitive Services
description: 了解“审阅”工具如何允许人工审查者审阅 Web 门户中的图像。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/15/2019
ms.author: sajagtap
ms.openlocfilehash: a482ecf4a0d321525ab7e392695d2c4c0eebeadc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628975"
---
# <a name="create-human-reviews"></a>创建人工审阅

在本指南中，您将了解如何设置[评审](../review-api.md#reviews)评审工具网站上。 评审存储和显示审阅人，若要评估的内容。 版主可以更改标签应用和应用根据其自己自定义标记。 当用户完成评审时，将结果发送到指定的回调终结点，并从站点中删除的内容。

## <a name="prerequisites"></a>必备组件

- 登录或在内容审查器上创建一个帐户[审阅工具](https://contentmoderator.cognitive.microsoft.com/)站点。

## <a name="image-reviews"></a>图像评审

1. 转到[审阅工具](https://contentmoderator.cognitive.microsoft.com/)，选择**尝试**选项卡，并上传一些图像以查看。
1. 一旦已上传的图像已完成处理，请转到**评审**选项卡并选择**映像**。

    ![显示审阅工具的 Chrome 浏览器，其中突出显示了“审阅图像”选项](images/review-images-1.png)

    图像显示与已分配由自动审查过程任何标签。 通过审阅工具已提交的映像不向其他审阅者可见。

1. （可选） 移动**评审可显示**滑块 (1)，可以调整在屏幕显示的图像数量。 单击**标记**或**未标记**要相应地进行排序的图像的按钮 (2)。 单击标记面板 (3) 来切换打开或关闭它。

    ![Chrome 浏览器显示带有标记图片的审阅工具以供审阅](images/review-images-2.png)

1. 若要查看有关映像的详细信息，请单击缩略图，然后选择省略号**查看详细信息**。 可以将图像分配给具有子**转为**选项 (请参阅[团队](./configure.md#manage-team-and-subteams)部分，了解有关子团队的详细信息)。

    ![突出显示“查看详细信息”选项的图像](images/review-images-3.png)

1. 浏览详细信息页面上的图像审阅信息。

    ![在单独窗格中列出审阅详细信息的图像](images/review-images-4.png)

1. 根据需要审核并更新标记分配后，单击“下一步”即可提交评论。 提交后，你有大约五秒的时间来单击“上一步”按钮返回上一屏幕并再次查看图像。 之后，图像不再位于“提交”队列中，并且“上一步”按钮不再可用。

## <a name="text-reviews"></a>文本评审

文本评审类似于图像评审。 而不是上传内容，您只需编写或粘贴文本 （最多 1024 个字符）。 然后，内容审查器分析文本，并应用 （除了其他审核信息，例如猥亵语言和个人数据） 的标记。 文本审查中您可以切换应用的标记和/或之前提交评审应用自定义标记。

![评审工具的屏幕截图，显示 Chrome 浏览器窗口中的已标记文字](../images/reviewresults_text.png)

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了如何设置和使用从内容审查器评审[审阅工具](https://contentmoderator.cognitive.microsoft.com)。 接下来，请参阅[REST API 指南](../try-review-api-review.md)或[.NET SDK 指南](../moderation-reviews-quickstart-dotnet.md)若要了解如何以编程方式创建评审。